+++
title = "go实现Event事件"
date = 2022-07-25T11:50:00+08:00
categories = ["golang"]
tags = ["golang"]
draft = false
summary = "go协程之间用通道通信，如何将它改造成事件形式。"
+++

### 描述

go协程之间用通道通信，如何将它改造成事件形式。


### 需求描述

以下是我的需求

```go
e := NewEvent()
//协程1等待结果, 10是等待时间
e.Wait(10)
//协程2发送结果
e.Send(xxxx)
```

### 原理

原理比较简单，不描述了

### 示例

```go
package common

import (
	"go.uber.org/zap"
	"sync"
	"time"
)

var NOT_USED interface{} = struct{}{}

func NewEvent() *Event {
	e := &Event{
		log: zap.S(),
	}
	e.Reset()
	return e
}

type Event struct{
	waiters  sync.Map
	log *zap.SugaredLogger
}

func (e *Event) Wait(timeout time.Duration) interface{}{
	ticker := time.NewTicker(time.Second*timeout)
	defer ticker.Stop()

	resultChan := make(chan interface{}, 1)
	e.waiters.Store(resultChan, true)

	select{
	case result := <- resultChan:
		if result == NOT_USED{
			return nil
		}
		return result
	case <- ticker.C:
		close(resultChan)
	}

	e.waiters.Delete(resultChan)
	return nil
}

func (e *Event) Send(result interface{}){
	e.waiters.Range(func(k, v interface{})bool{
		resultChan := k.(chan interface{})
		select{
		case resultChan <- result:
			e.waiters.Delete(resultChan)
		default:
			e.log.Warnf("Event.Send %p resultChan=%d, result=%v", e, len(resultChan), result)
		}
		return true
	})
}

func (e *Event) Reset(){
	e.waiters.Range(func(k, v interface{})bool{
		resultChan := k.(chan interface{})
		resultChan <- NOT_USED
		e.waiters.Delete(resultChan)
		return true
	})
}
```

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)