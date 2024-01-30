+++
title = "celery变量共享"
date = 2022-07-25T12:12:00+08:00
summary = "很多情况下我们想让task共享变量，该怎么做?"
categories = ["python"]
tags = ["python", "celery"]
draft = false
+++

### 问题

很多情况下我们想让task共享变量，该怎么做?

### celery的并发原理

celery的并发任务池，有eventlet, gevent, prefork, thread类型

eventlet/gevent协程: 只有一个进程一个线程, 全局变量在task之间共享
    
prefork属于multiprocessing: multiprocessing全局变量也是共享的

thread多线程: 全局变量共享
    
### 验证方法

用ab命令模拟大量并发，很容易测试出来

```
ab -n 1000 -c 100 -p ./post.txt -T application/json http://xxxx:5000/xxx
```

### 结论

1. celery如果访问数据库, gpu等资源, 不用担心多次加载

2. 注意: 如果在task中初始化全局变量, 初始化较慢, 同时又收到大量task请求，可能会导致初始化多次
    

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)