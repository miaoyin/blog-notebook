+++
title = "go如何实现并发安全的优先级队列"
date = 2022-07-25T11:49:00+08:00
categories = ["golang"]
tags = ["golang"]
draft = false
summary = "go的如何实现优先级队列？"
+++

### 描述

go的如何实现优先级队列？

优先级别队列是否并发安全？

### 我的需求场景

实现了一个通信协议，收发消息。发送通道会阻塞。如果拥堵时，要控制拥堵消息的优先顺序。

优先级用Priority加上时间, Priority小优先, 早到优先。


### 原理

1. 通过实现官方接口heap, 得到优先级队列

2. 使用用chan进行控制并发控制，达到并发安全的效果


### 优先队列实现

```go
package queue

import (
	"xxxx/packets"
	"container/heap"
	"time"
)

//---------------------------------------
//		优先队列(提供并发安全接口)
//---------------------------------------

func NewPriorityQueue() *PriorityQueue{
	q := &PriorityQueue{
		isPushing: false,
		ch: make(chan *QueueItem, 1),
	}
	return q
}


type PriorityQueue struct{
	//队列(最小堆)
	items []*QueueItem	//堆
	ch chan *QueueItem	//同步控制
	isPushing bool	//推送状态
}

// 数据项
type QueueItem struct {
	Value    	*packets.Packet	// value
	Priority 	int    				// 优先级(越小越靠前)
	JoinTime    time.Time			// 加入时间
	index 		int  				// 索引
}

//---------------------------------------
//		heap接口
//---------------------------------------
func (pq PriorityQueue) Len() int { return len(pq.items) }

func (pq PriorityQueue) Less(i, j int) bool {
	// priority越小越靠前
	if pq.items[i].Priority != pq.items[j].Priority{
		return pq.items[i].Priority < pq.items[j].Priority
	}else{
		return pq.items[i].JoinTime.Sub(pq.items[j].JoinTime) <0
	}
}

func (pq PriorityQueue) Swap(i, j int) {
	pq.items[i], pq.items[j] = pq.items[j], pq.items[i]
	pq.items[i].index = i
	pq.items[j].index = j
}

func (pq *PriorityQueue) Push(x interface{}) {
	n := len(pq.items)
	item := x.(*QueueItem)
	item.index = n
	pq.items = append(pq.items, item)
}

func (pq *PriorityQueue) Pop() interface{} {
	old := *pq
	n := len(old.items)
	if n==0{
		return nil
	}
	item := old.items[n-1]
	old.items[n-1] = nil  // avoid memory leak
	item.index = -1 // for safety
	pq.items = old.items[0 : n-1]
	return item
}

// update modifies the priority and value of an Item in the queue.
func (pq *PriorityQueue) update(item *QueueItem, value *packets.Packet, priority int) {
	item.Value = value
	item.Priority = priority
	heap.Fix(pq, item.index)
}

//---------------------------------------
//		同步接口
//---------------------------------------
func (pq *PriorityQueue) Get(timeout time.Duration) *QueueItem{
	timeout |= 30

	ticker := time.NewTicker(time.Second*timeout)
	defer ticker.Stop()

	select {
	case result := <- pq.ch:
		return result
	case <- ticker.C:
	}
	return nil
}

func (pq *PriorityQueue) PutNoWait(item *QueueItem){
	heap.Push(pq, item)
	//启用推送
	pq.startPushing()
}

//负责推送数据
func (pq *PriorityQueue) startPushing(){
	if pq.isPushing{
		return
	}
	go pq._startPushing()
}

func (pq *PriorityQueue) _startPushing(){
	for pq.Len()>0{
		pq.isPushing = true

		x := heap.Pop(pq)
		if x==nil{
			continue
		}
		item := x.(*QueueItem)
		pq.ch <- item
	}
	pq.isPushing = false
}

```

### 用法示例


```go
//并发安全用法
func TestSendQueue(t *testing.T){
	q := NewPriorityQueue()
	q.PutNoWait(&QueueItem{
		Value: 	&packets.Packet{
						Header: &packets.SecsHeader{
						System: 1,
					},
					Data: []byte{},
				},
		Priority: 100,
		JoinTime: time.Now(),
	})
    item := q.Get(30)
    fmt.Println(item)
}
```

### 总结

上面已经满足我的需求场景了。 队列的删除、撤销，并不是我的应用场景，不在这里考虑，也不是队列该做的事。


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)