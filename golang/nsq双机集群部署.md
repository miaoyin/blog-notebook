### 问题

双机nsq如果部署集群，如何保证高可用性

1. 单节点nsqlookup故障?

2. 单节点nsq故障?

3. 消息丢失?

### 环境说明

两台机器

机器A 192.168.120.1

机器B 192.168.120.101

### 部署过程

* 部署方法

1. 机器A部署一套nsqlookup+nsqd

2. 机器B部署一套nsqlookup+nsqd

3. 生产者将消息同时写入两个nsqd

3. 消费者监听两个nsqlookup

* 结构图

![结构图](../../static/nsq集群结构图.jpg)

* 机器A

```bash
nsqlookupd -broadcast-address 192.168.120.1
nsqd -lookupd-tcp-address=192.168.120.1:4160 -lookupd-tcp-address=192.168.120.101:4160 -broadcast-address 192.168.120.1
```

* 机器B

```bash
nsqlookupd -broadcast-address 192.168.120.101
nsqd -lookupd-tcp-address=192.168.120.1:4160 -lookupd-tcp-address=192.168.120.101:4160 -broadcast-address 192.168.120.101
```

### pynsq测试脚本

* 生产者

```python
import nsq
import tornado.ioloop
import time

def pub_message():
    writer.pub('test', str(time.strftime('%H:%M:%S')).encode("utf-8"), finish_pub)

def finish_pub(conn, data):
    print(data)

# 写入两个nsq好处: 1.防止nsqd单点故障  2.防止消息丢失
writer = nsq.Writer(['192.168.120.101:4150', '192.168.120.1:4150'])

tornado.ioloop.PeriodicCallback(pub_message, 1000).start()
nsq.run()
```

* python消费者

```python
import nsq


def handler(message):
    print(message, message.id, message.timestamp, message.attempts, message.body)
    return True

# 防止nsqlookup故障
r = nsq.Reader(message_handler=handler,
               lookupd_http_addresses=['http://192.168.120.1:4161', 'http://192.168.120.101:4161'],
               topic='test', channel='abc', lookupd_poll_interval=15)

nsq.run()
```

### 总结

只要有一个nsqlookup和一个nsqd存活，系统就不会挂


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://doc.sxjy360.top/book/佛說大乘無量壽莊嚴清淨平等覺經(難字注音).pdf)
[净土大经科注2014-doc](http://doc.sxjy360.top/book/净土大经科注2014-doc.zip)
[此生必看的科学实验-水知道答案](http://v.youku.com/v_show/id_XMjgzMzcwNDk4OA)
[印光大师十念法（胡小林主讲第1集）](http://v.youku.com/v_show/id_XMzUwMzc4NzY4NA)
