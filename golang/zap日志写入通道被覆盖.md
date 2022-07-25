### 描述

实现了一个io.Writer接口，允许zap日志写入，传递给界面，出现了日志被覆盖、重复的情况。

### 原因

io.Writer的接口Write传入的[]byte真实类型是slice。通道传递slice时，用的是引用传递，所以真实数据有被覆盖的情况 

### 示例

* zap初始化

```go
...
out = COut()
zapcore.NewCore(encoder, zapcore.AddSync(out), clevel),
...
```

* 自定义日志写入Channel

```go
package logs

var _GlobalChanOutput = &ChanOutput{
	outs: make([]chan *[]byte, 0),
}

/*---------------------------------------
	全局函数
示例
	logs.COut().AddOut(xx)
---------------------------------------*/

//全局ChanOutput
func COut() *ChanOutput{
	return _GlobalChanOutput
}

/*---------------------------------------
	log to channel
---------------------------------------*/

type ChanOutput struct{
	outs []chan *[]byte
}

//io.Writer接口
func (c *ChanOutput) Write(p []byte) (n int, err error) {
	count :=0

	//数据复制(防止被覆盖)
	_p := make([]byte, len(p))
	copy(_p, p)

	for _, out :=range c.outs{
		select{
		case out <- &_p:
			count += 1
		default:
		}
	}
	return count, nil
}

func (c *ChanOutput) AddOut(out chan *[]byte) {
	c.outs = append(c.outs, out)
}

```

* 读取日志

```go
logOut := make(chan *[]byte, 1024)
logs.COut().AddOut(logOut)

pctx, pctxCancel := context.WithCancel(context.Background())
go func(ctx context.Context){
    select {
    case msg := <- logOut:
        fmt.Println(string(*msg))
    case <-ctx.Done():
        break
    }
}(pctx)
```

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)
