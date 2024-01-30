+++
title = "influxdb遇到时间点存储不了"
date = 2022-07-25T12:03:00+08:00
categories = ["golang"]
tags = ["golang", "influxdb"]
draft = false
summary = "有一份mysql存储的历史数据，准备把它转为influxdb存储, 时间点用的是mysql表中记录的创建时间，创建时间是时许的。"
+++

### 问题描述

    有一份mysql存储的历史数据，准备把它转为influxdb存储, 时间点用的是mysql表中记录的创建时间，创建时间是时许的。

### 环境描述

* influxdb: 2.0.8

* 开发语言: go

* influx客户端: influxdb-client-go 2.5.0


### 关键代码摘要

    使用如下代码, 创建point，发现存储不了。

```
point := influxdb2.NewPoint("table_name",
    map[string]string{
        ...
    },
    map[string]interface{}{
        ....
    },
    ts,     //对应mysql表中记录的创建时间
    )
```

### 解决过程

    开始以为数据是几年前的, 时间太长存储不了。 后来用time.Time().Add(-xxx), 把时间移到3年前， 结果能存储


### 解决办法

    原因是influx的精度是纳秒, 存入的时间精度是秒, 在Influx中无法存储

    解决办法是加1纳秒, ts.Add(time.Nanosecond*1)

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)
