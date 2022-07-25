### 描述

* go的官方库计时器, 有一个陷阱，ticker.Stop()如果不注意会导致协程无法关闭。
* 如果我们想强制中断计时，该如何做？

### 示例：ticker.Stop()会导致协程无法结束

下面示例便于理解

```go

func main(){
    ticker := time.NewTicker(time.Second*10)
    go func(){
        log.Println("go start")
        select{
        case <-ticker.C:
            log.Println("ticker stop")
        }
        //会发现这一句化一直不打印
        log.Println("go end")
    }()
    time.Sleep(1)
    //stop无法结束上面的协程
    ticker.Stop()
    time.Sleep(20)
    log.Println("main end")
}


```


### 使用标记值终止计时器

```go

func WaitResponse(queueChan *chan Packet, timeout time.Duration) *Packet, error{
    ticker := time.NewTicker(time.Second*timeout)
    defer ticker.Stop()

	var response Packet

	var err error
	select{
	case response = <-*queueChan:
        if response == "标记值xxx"{
            return nil, errors.New("terminate response")
        }
	case <-ticker.C:
		err = errors.New("wait response timeout")
	}
	return &response, err
}

go WaitResponse(queueChan, time.Duration(60))

queueChan <- "标记值xxx"
```

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://doc.sxjy360.top/book/佛說大乘無量壽莊嚴清淨平等覺經(難字注音).pdf)
[净土大经科注2014-doc](http://doc.sxjy360.top/book/净土大经科注2014-doc.zip)