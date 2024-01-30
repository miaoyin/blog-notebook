+++
title = "golang实现单例模式"
date = 2022-07-25T11:44:00+08:00
categories = ["golang"]
tags = ["golang", "sync"]
draft = false
summary = "go已经提供了实现单例模式的工具sync.Once"
+++

### golang实现单例模式

go已经提供了实现单例模式的工具sync.Once

### 示例

借用sync.Once的一次性特点

```go
import (
    "sync"
)

type singleton struct {
}

var _instance *singleton
var _once sync.Once

func GetInstance() *singleton {
	_once.Do(func() {
		instance = &singleton{}
	})
	return instance
}
```

### once原理

用变量done的指针地址作为key，标记在atmoic中，同时借用atomic的原子特性, 达到线程安全的效果。

```go
package sync

import (
	"sync/atomic"
)


type Once struct {
	done uint32
	m    Mutex
}

func (o *Once) Do(f func()) {

	if atomic.LoadUint32(&o.done) == 0 {
		// Outlined slow-path to allow inlining of the fast-path.
		o.doSlow(f)
	}
}

func (o *Once) doSlow(f func()) {
	o.m.Lock()
	defer o.m.Unlock()
	if o.done == 0 {
        //取done的索引地址作为key保存
		defer atomic.StoreUint32(&o.done, 1)
		f()
	}
}
```

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)