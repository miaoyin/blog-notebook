### 说明

rpc的实现方式有很多种, 两者没有可比性。

情况是我需要用python做远程调用, 分离子系统, 选择celery还是rpc框架？


### rpc典型应用openstack

openstack的oslo.messaging实现rpc, 基于Kombu和rabbitmq实现远程调用

celery同样可以基于Kombu和rabbitmq实现远程调度

两者从底层上来看似乎没什么不同


### 结论

单纯从底层原理上看, celery用来代替rpc做远程调用，似乎没什么问题

[此生必看的科学实验-水知道答案](http://v.youku.com/v_show/id_XMjgzMzcwNDk4OA)
[印光大师十念法（胡小林主讲第1集）](http://v.youku.com/v_show/id_XMzUwMzc4NzY4NA)