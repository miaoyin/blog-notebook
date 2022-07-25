### 问题描述

    influx查询包含了水平拆分、垂直拆分. 查询结果的每一条记录record，只对应一个field

    写入一个point时包含多个field, 查询时如何将这几个field一起查出来呢？

### 写入示例

```go
point := influxdb2.NewPoint("history",
    map[string]string{
        "Version": DedefaultVersion,
        "Name": sh.Name,
        "Active": fmt.Sprintf("%v", sh.Active),
        "PortIdx": fmt.Sprintf("%d", sh.PortIdx),
        "Port": fmt.Sprintf("%v", sh.Port),
    },
    map[string]interface{}{
        "MsgId": fmt.Sprintf("%d", sh.MsgId),
        "Content": sh.Content,
    },
    time.Now(),
    )
```

* 查询

```
from(bucket:"my-bucket")
|> range(start: -10m)
|> filter(fn: (r) => (r._measurement=="history") and (r.Name=="xxxx") )
```

用这个方法查询, 每个record只包含一个_field


### 解决方法: 分组

    分组之后的record，会多一个table属性。table相同，则为一组。也就是一个point的field。

* 按写入时间分组

```go
|> group(columns: ["_time"])
```

* 增加id标签，按id分组

为每个point增加一个id标签, 然后按id分组



[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)
