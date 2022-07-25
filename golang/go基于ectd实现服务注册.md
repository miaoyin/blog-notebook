
### 描述

    基于etcd实现go程序的服务注册，所看的学习资料均比较简单, 实际中要经过反复测试，自己写了一个先用着，有坑再填.


### 需求

```
1. 注册一个服务: 程序启动时
2. 注销服务: a. 程序异常退出时, 自动注销, 有5秒TTL延迟; b. 调用Stop()接口主动注销
3. 健壮性: a.ectd单节点重启, ectd本身支持; b. etcd全部重启后, 能够恢复正常
4. key的格式: 前缀/ip/pid
5. value: 字符串.(可以先转为json, 再转string)
```


### 创建etcd客户端

```go
//不是重点, 摘要如下代码

cli, err := clientv3.New(clientv3.Config{
		Endpoints:   ec.Endpoints,
		DialTimeout: 5 * time.Second,
	})
	if err != nil {
		log.Fatal(err)
	}
```

### 创建Service对象, 包含服务注册

```go
package main

import (
	"context"
	"errors"
	"fmt"
	clientv3 "go.etcd.io/etcd/client/v3"
	"go.etcd.io/etcd/client/v3/clientv3util"
	"go.uber.org/zap"
	"go.uber.org/zap/zapcore"
	"net"
	"os"
	"strings"
	"sync"
	"test/etcd"
	"time"
)


var IP string
var PID int


func init(){
	//日志
	encoderConfig := zap.NewProductionEncoderConfig()
	encoderConfig.EncodeTime = zapcore.ISO8601TimeEncoder
	encoderConfig.EncodeLevel = zapcore.CapitalLevelEncoder
	encoder := zapcore.NewConsoleEncoder(encoderConfig)

	//创建
	var clevel zapcore.Level
	clevel.Set("debug")
	log := zap.New(zapcore.NewCore(encoder, zapcore.AddSync(os.Stdout), clevel))
	//设为全局
	zap.ReplaceGlobals(log)

	//
	IP, _ = GetLocalIP()
	PID = os.Getpid()
}

func GetLocalIP() (string,error){
	addrs, err := net.InterfaceAddrs()
	if err != nil {
		zap.S().Warn(err)
		return "", err
	}
	for _, addr := range addrs{
		if ipnet, ok := addr.(*net.IPNet); ok && !ipnet.IP.IsLoopback(){
			if ipnet.IP.To4() != nil {
				return ipnet.IP.String(), nil
			}
		}
	}
	return "", errors.New("unable to determine local ip")
}


func RegisterService(endpoints []string, key string, value string) *Service{
	s := &Service{
		Value: value,
		endpoints: endpoints,
	}

	s.TTL = 5
	s.Key = strings.Join([]string{key, IP, fmt.Sprintf("%d", PID)}, "/")

	s.init()

	return s
}


type Service struct{
	Key          	string		//key 格式: /前缀/名称/ip/pid
	Value           string		//存放信息
	endpoints    	[]string	//etcd地址
	//心跳
	IsAlive      	bool		//是否存活
	heartCancel 	context.CancelFunc	//取消心跳
	//租约
	TTL             int64		//租约(秒)
	grant			*clientv3.LeaseGrantResponse	//租约
}


func (s *Service) init(){
	//启用etcd连接
	etcd.EnableEtcd(s.endpoints)

	//心跳
	go s.start()
}

func (s *Service) start(){
	defer func(){
		s.IsAlive = false

		//注销服务
		s.deleteService()
		//关闭续约(如果共用会误关?)
		etcd.Cli().Lease.Close()
	}()

	var aliveRsp <-chan *clientv3.LeaseKeepAliveResponse
	var err error
	var ctx context.Context
	for{
		if !s.IsAlive{
			// 注册服务
			s.registerService()

			// 续期信号chan
			aliveRsp, err = etcd.Cli().KeepAlive(context.TODO(), s.grant.ID)
			if err != nil{
				zap.S().Warn(err)
			}

			ctx, s.heartCancel = context.WithCancel(context.Background())
		}

		// 监听心跳信号
		select{
		case rsp := <- aliveRsp:
            //异常时无信号
			if rsp ==nil{
				s.IsAlive = false
				zap.S().Info("service missing signal")
				time.Sleep(time.Second*5)
				continue
			}

			s.IsAlive = true
			zap.S().Debugf("service alive %v", rsp.ID)
		case <- ctx.Done():
			zap.S().Info("service stopping")
			return
		}
	}
}

//停止

func (s *Service) Stop(){
	s.heartCancel()
}

//注册服务

func (s *Service) registerService() bool{
	cli :=  etcd.Cli()
	s.grant, _ = cli.Grant(context.TODO(), s.TTL)

	zap.S().Infof("start register service %s", s.Key)
	txn := cli.Txn(context.TODO()).If(clientv3util.KeyMissing(s.Key)).
		Then(clientv3.OpPut(s.Key,s.Value, clientv3.WithPrevKV(), clientv3.WithLease(s.grant.ID)))
	txRsp, err := txn.Commit()
	if err!=nil{
		zap.S().Warn(err)
		return false
	}

	zap.S().Infof("register success %v", txRsp.Succeeded)

	// 已存在key 则succee=false
	return txRsp.Succeeded
}

//删除服务
func (s *Service) deleteService(){
	_, err := etcd.Cli().Delete(context.TODO(),s.Key)
	if err!=nil{
		zap.S().Warn(err)
	}
}

//main
func main() {
    //注册一个服务
	s := RegisterService([]string{"10.4.120.101:2379", "10.4.120.101:12379", "10.4.120.101:22379"}, "xxx/xxx", "11111")

	// 测试线程, 定时检查状态
	go func(){
		i := 0
		for{
			fmt.Println("service alive =", s.IsAlive)
			i +=1
			time.Sleep(time.Second*1)
            //模拟停止
			//if i ==15{
			//	s.Stop()
			//}
		}
	}()

	var gc sync.WaitGroup
	gc.Add(1)
	gc.Wait()
}

```

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)