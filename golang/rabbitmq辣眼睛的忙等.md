### 背景

用golang做了一个分布式结构的系统, 消息服务器用了rabbitmq。

整体是基于machinery+rabbitmq形式的分布式任务系统

在做压力测试时，发现rabbitmq的cpu使用率飙升


### 分析

* 官方给出的解释

scheduler采用了忙等，来提高消息传递性能

https://www.rabbitmq.com/runtime.html#cpu

其中也讲了一些方法来降低cpu使用率

* 官方建议

1. rabbtimq最好是装在单独的服务器，不与其它应用共用。

2. 也介绍了修改cpu使用个数的方法

* 对我而言

通信关cpu什么事，导致cpu飙升，就是不应该

* 站在传统语言java/python/...等语言角度

忙等好像也没啥

* 站在go的角度

忙等是上世纪的技术了。

看起来太辣眼睛。

* 从我的应用场景上来看

系统规模可大可小, 而且业务高峰时间很短

在系统规模小时，不大愿意单独给rabbitmq一台服务器

在业务高峰的这几分钟，正好会影响其它系统

* 是否有其它选择

nats

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://doc.sxjy360.top/book/佛說大乘無量壽莊嚴清淨平等覺經(難字注音).pdf)
[净土大经科注2014-doc](http://doc.sxjy360.top/book/净土大经科注2014-doc.zip)
