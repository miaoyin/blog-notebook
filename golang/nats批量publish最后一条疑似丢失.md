+++
title = "nats批量publish最后一条疑似丢失"
date = 2022-07-25T12:03:00+08:00
categories = ["golang"]
tags = ["golang", "nats-server"]
draft = false
summary = "写了一个测试脚本，从数据读取一组数据，然后逐条publish到nats-server中, 确定每条都publish了, 有另外一个程序中subscribe订阅，在该程序发现最后一条一直没收到"
+++

### 问题描述

    写了一个测试脚本，从数据读取一组数据，然后逐条publish到nats-server中, 确定每条都publish了

    有另外一个程序中subscribe订阅，在该程序发现最后一条一直没收到


### 环境

* go: 1.16

* nats-server

### 程序摘要

* 发布

```go
for _, record := range records{
    ...
    gNatsConn.Publish(Topic, record)
    time.Sleep(time.Second*0.1)    //间隔越小出现的概率越大. 间隔1秒好像就没有
}
```

* 订阅

```go
natsConn.Subscribe(topic, func(m *nats.Msg){
    fmt.Println(m.Data)
    ....
}
```

### 原因以及解决办法

    nats在发布消息时，有应该是用了缓存通道, 大小是1。最后一条数据有可能没被读走

    发布频率较高，最后需要flush一下


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)