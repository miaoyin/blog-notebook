+++
title = "celery代替rpc做远程调用是否可行"
date = 2022-07-25T12:12:00+08:00
summary = "rpc的实现方式有很多种, 两者没有可比性。 情况是我需要用python做远程调用, 分离子系统, 选择celery还是rpc框架？"
categories = ["python"]
tags = ["python", "celery", "rpc"]
draft = false
+++

### 说明

rpc的实现方式有很多种, 两者没有可比性。

情况是我需要用python做远程调用, 分离子系统, 选择celery还是rpc框架？


### rpc典型应用openstack

openstack的oslo.messaging实现rpc, 基于Kombu和rabbitmq实现远程调用

celery同样可以基于Kombu和rabbitmq实现远程调度

两者从底层上来看似乎没什么不同


### 结论

单纯从底层原理上看, celery用来代替rpc做远程调用，似乎没什么问题

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)