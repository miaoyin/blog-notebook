+++
title = "go实现Event事件模型修正"
date = 2022-07-25T11:52:00+08:00
categories = ["golang"]
tags = ["golang"]
draft = false
summary = "之前文章中实现了event事件模型，实际使用中发现了几个问题"
+++

### 描述

之前文章中实现了event事件模型，实际使用中发现了几个问题


1. 并发会出现情况send先执行, 此时还没有waiter。将wait拆分成addWaiter和wait()

2. 不能使用缓冲通道。会出现send完成,wait还未收到的情况。

### 用法修正

```go
e.Reset()
waiter := e.AddWaiter()
waiter := e.Wait(waiter, xxx)
e.Send(xxx)
```

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


func (e *Event) AddWaiter() *chan interface{}{
	//等待者
	resultChan := make(chan interface{}, 0)
	e.waiters = append(e.waiters, resultChan)
	return &resultChan
}

//等待结果
func (e *Event) Wait(waiter *chan interface{}, timeout time.Duration) (interface{}, error){
	if e.result ==NOT_USED{
		ctx, cancel := context.WithTimeout(e.ctxBg, time.Second*timeout)
		defer cancel()

		//等待
		select{
		case result := <- *waiter:
			return result, nil
		case <- ctx.Done():
			if ctx.Err() == context.Canceled{
				return nil, nil
			}
			return nil, errors.New("event wait timeout")
		}
		return nil, nil
	}else{
		return e.result, nil
	}
}

//发送结果
func (e *Event) Send(result interface{}) error{
	//防止发送多次
	if e.result !=NOT_USED{
		return errors.New("Event is used")
	}

	ctx, cancel := context.WithTimeout(e.ctxBg, time.Second*3)
	defer cancel()

	for _, resultChan := range e.waiters{
		select{
		case resultChan <- result:
		case <- ctx.Done():
			e.log.Warnf("Event.Send %p resultChan=%d, result=%v", e, len(resultChan), result)
		}
	}
    e.result = result
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