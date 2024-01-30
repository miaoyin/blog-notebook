+++
title = "nsq.reader错误connection closed"
date = 2022-07-25T12:03:00+08:00
categories = ["golang"]
tags = ["golang", "nsq"]
draft = false
summary = "学习官网例子时，会碰到的一个小错误"
+++

### 描述

学习官网例子时，会碰到的一个小错误. 过程描述:

1. 按照nsq官网，搭建一个nsq小集群

2.使用python客户端pynsq编写测试客户端

3. 如果上面过程在一台机子上完成，不会有问题

4. 如果客户端与nsq不在一台机子上，会出现下面错误

### 错误内容

```
WARNING:nsq.reader:[localhost.localdomain:4150:test:abc] connection closed
```


### python消费客户端

```python
import nsq

def handler(message):
    print(message, message.body)
    return True


r = nsq.Reader(message_handler=handler,
               lookupd_http_addresses=['http://192.168.120.101:4161'],
               topic='test', channel='abc', lookupd_poll_interval=15)

nsq.run()
```

## python生产客户端

```python
import nsq
import tornado.ioloop
import time

def pub_message():
    writer.pub('test', str(time.strftime('%H:%M:%S')).encode("utf-8"), finish_pub)

def finish_pub(conn, data):
    print(data)

writer = nsq.Writer(['127.0.0.1:4150'])

tornado.ioloop.PeriodicCallback(pub_message, 1000).start()
nsq.run()
```

### 正确方法

启动nsqd时，指定nsqd的广播ip，也就是其它机子可以访问的ip，而非默认的localhost。例如："-broadcast-address 192.168.120.101"

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)
