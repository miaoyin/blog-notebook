+++
title = "nats-server系统服务只能使用sc命令注册"
date = 2022-07-25T12:03:00+08:00
categories = ["golang"]
tags = ["golang", "nats-server"]
draft = false
summary = "windows环境下nats-server注册成系统, 官方推荐是sc命令, 而我觉得sc命令台太麻烦"
+++


### 起因描述

windows环境下nats-server注册成系统, 官方推荐是sc命令

而我觉得sc命令台太麻烦, 为什么不用github.com/kardianos/service，或者更方便的工具呢?

### 方法一：使用nssm通过界面进行配置

1. 下载地址

http://nssm.cc/download

2. 过程省略

3. 结果死活不行，由于是第三方工具，出错找不到原因，放弃


### 方法二：通过服务外壳作为系统服务

* 介绍

使用go生成一个demo.exe，再将demo.exe注册成系统，demo.exe读取配置文件，然后demo.exe通过exec.Command启动nats-server.exe

go比较好用的库可以用github.com/kardianos/service


* demo.exe启动nats-server.exe关键源码

```go
p.cmd = exec.Command(fullExec, p.Args...)
p.cmd.Dir = p.Dir
p.cmd.Env = append(os.Environ(), p.Env...)
```

* demo.json配置示例

```json
{
	"Name": "demo",
	"DisplayName": "demo",
	"Description": "demo for nats-server",
	
	"Dir": "d:\\nats-server",
	"Exec": "xxxx\\nats-server.exe",
	"Args": ["-c ","xxx\\nats-server.conf"],
	"Env": [
	],
	
	"Stderr": "C:\\log\\nats_err.log",
	"Stdout": "C:\\log\\nats_out.log"
}
```

* 结果报错

```shell
The service process could not connect to the service controller.
```

意思是一个服务不能连接到另一个服务，啥意思呢？ 也就是通过exec.Command启动的竟然是服务，不应该是exe么.

* nats-server启动源码分析

nats-server.exe的启动入口源码

```go
func Run(server *Server) error {
    //入口1：docker
	if dockerized {
		server.Start()
		return nil
	}
	isInteractive, err := svc.IsAnInteractiveSession()
	if err != nil {
		return err
	}
    //入口2: 交互模式
	if isInteractive {
		server.Start()
		return nil
	}
    //入口3: 启动服务(serviceName被写死了，只能是nats-server)
	return svc.Run(serviceName, &winServiceWrapper{server})
}
```

* 错误原因总结

当以外壳方式启动时，走的是"入口3"，也是启动服务，如果没有nats-server，它就啥事也没干，结果nats-server并没有启动

### 方法三：sc命令

官方推荐

###  总结

1、nat-server只能以sc命令注册成服务。怕麻烦可以写一个bat。

2、nats-server服务名只能是nats-server


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)

