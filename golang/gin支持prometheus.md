+++
title = "gin支持prometheus"
date = 2022-08-03T15:25:00+08:00
categories = ["golang"]
tags = ["gin", "prometheus", "grafana", "metrics", "golang"]
draft = false
summary = "influxdb的docker-compose如何配置"
+++

### 起因

gin使用了微服务架构, 如何将自己的服务都监控起来?

### 疑问列表


1. grafana是什么, 能做什么?

grafana是一个监控平台，支持不同数据源的可视化。也就是说支持从prometheuse,influxdb等数据源, 并将他们的数据形式可视化的图表.

2. prometheus能做什么?
prometheuse是一个监控系统,通过定时http pull采集数据. 并且支持http push.

3. prometheus和grafana是什么关系?
prometheuse是监控系统, 界面展示比较粗糙, grafana主要是可视化, 并且支持多种类型数据源。

4. gin如何与prometheuse结合?
见后面

5. gin如何与grafana?
grafana不支持直接接入gin服务。需要gin先接入prometheuse，再将prometheus接入grafana。

6. gin服务的metrics数据格式如何解析?
暂时未找到

7. gin自定义监控项
略. 不是本文主题


### 下载安装

```
grafana
https://github.com/grafana/grafana

prometheus
https://github.com/prometheus/prometheus
```

### gin支持prometheuse

1. 修改gin服务代码

```go
import (
    ...
	"github.com/gin-gonic/gin"
	"github.com/prometheus/client_golang/prometheus/promhttp"
    ...
)


//metrics, promhttp默认可以查看cpu,mem,线程等信息
router.GET("/metrics", gin.WrapH(promhttp.Handler()))
```

2.修改prometheuse配置(prometheuse.yml)

```yaml
scrape_configs:
  - job_name: "prometheus-demo"
    static_configs:
      - targets: ["192.168.28.26:8011"]
```

3. 启动服务

a. 启动gin和prometheuse服务后, 访问localhost:9090

b. 在"Expression"输入框选择"go_goroutines", 点击"Execute"

c. 刚开始数据比较少,可以过一段时间后再刷新页面

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)

[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)

[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)

[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)