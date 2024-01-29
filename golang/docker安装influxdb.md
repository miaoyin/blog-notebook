+++
title = "docker安装influxdb"
date = 2022-07-25T11:40:00+08:00
categories = ["docker"]
tags = ["influxdb", "docker", "docker-compose"]
draft = false
summary = "influxdb的docker-compose如何配置"
+++

influxdb的docker-compose如何配置

1. 新建数据目录

```shell
mkdir data-volumne
```

2. 生成配置文件

```shell
docker run --rm influxdb:2.0.8 influxd print-config > config.yml
```

3. docker-compose配置

docker-compose.yaml内容

```yaml
version: "2"
services:
   influxdb:
       image: influxdb:2.0.8
       volumes:
           - ./data-volumne:/var/lib/influxdb2
           - ./config.yml:/etc/influxdb2/config.yml
           - /usr/share/zoneinfo/Asia/Shanghai:/etc/localtime
       ports:
           - 8086:8086
       environment:
           DOCKER_INFLUXDB_INIT_MODE: setup
           DOCKER_INFLUXDB_INIT_USERNAME: my-user
           DOCKER_INFLUXDB_INIT_PASSWORD: my-password
           DOCKER_INFLUXDB_INIT_ORG: my-org
           DOCKER_INFLUXDB_INIT_BUCKET: my-bucket
```

4. 启动

docker-compose up

5. 目录结构如下

```shell
[root@localhost influxdb]# tree
.
├── config.yml
├── data-volumne
│   ├── engine
│   │   └── data
│   └── influxd.bolt
└── docker-compose.yaml
```


* 注意

上面修改influx时区无效，好像不能修改时区，默认都是UTC

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)

[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)

[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)

[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)