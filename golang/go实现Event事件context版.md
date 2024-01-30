+++
title = "go实现Event事件context版"
date = 2022-07-25T11:50:00+08:00
categories = ["golang"]
tags = ["golang"]
draft = false
summary = "上一篇文章，写了go如何实现Event事件，有几个可以优化的地方."
+++

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

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)