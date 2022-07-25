### 描述

上一篇文章，写了go如何实现Event事件，有几个可以优化的地方.
sync.Map保存结果可以不用这么复杂.
改用context关闭通道更加方便


### 事件实现

```go
package common

import (
	"context"
	"errors"
	"go.uber.org/zap"
	"time"
)
var NOT_USED = struct{}{}

func NewEvent() *Event {
	e := &Event{
		log: zap.S(),
	}
	e.Reset()
	return e
}

type Event struct{
	//监听者
	waiters  []chan interface{}
	//结果
	result interface{}
	//上下文控制
	ctxBg 	context.Context
	ctxCancel context.CancelFunc
	//日志
	log *zap.SugaredLogger
}

//等待结果
func (e *Event) Wait(timeout time.Duration) interface{}{
	ctx, cancel := context.WithTimeout(e.ctxBg, time.Second*timeout)
	defer cancel()

	//等待者
	resultChan := make(chan interface{}, 1)
	e.waiters = append(e.waiters, resultChan)

	//等待
	select{
	case result := <- resultChan:
		return result
	case <- ctx.Done():
	}
	return nil
}

//发送结果
func (e *Event) Send(result interface{}) error{
	//防止发送多次
	if e.result !=NOT_USED{
		return errors.New("Event is used")
	}

	e.result = result
	for _, resultChan := range e.waiters{
		select{
		case resultChan <- result:
		default:
			e.log.Warnf("Event.Send %p resultChan=%d, result=%v", e, len(resultChan), result)
		}
	}
	return nil
}

//重置
func (e *Event) Reset(){
	if e.ctxBg !=nil{
		e.ctxCancel()
	}

	e.ctxBg, e.ctxCancel = context.WithCancel(context.Background())
	e.waiters = nil
	e.result = NOT_USED
}
```

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://doc.sxjy360.top/book/佛說大乘無量壽莊嚴清淨平等覺經(難字注音).pdf)
[净土大经科注2014-doc](http://doc.sxjy360.top/book/净土大经科注2014-doc.zip)