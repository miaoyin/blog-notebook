+++
title = "traefik入门示例解析01"
date = 2023-05-15T16:46:00+08:00
summary = "微服务架构产生了大量的服务, 借助etcd之类的注册中心，找服务还需要接入etcd，还是比较麻烦，有没有统一的网关入口？"
categories = ["middleware"]
tags = ["traefik", "docker-compose", "middleware"]
draft = false
+++


### traefik是什么

微服务架构产生了大量的服务, 借助etcd之类的注册中心，找服务还需要接入etcd，还是比较麻烦，有没有统一的网关入口？

下面借助简单例子, 试用并理解traefik。

### 官方示例

官网提供了docker-compose示例, 我改了几个端口方便理解.

* docker-compose.yaml

```yaml
version: "3"
services:
  reverse-proxy:
    #image: traefik:v2.10
    image: traefik:latest
    ports:
      # 提供http入口
      - "81:80"
      # web界面服务
      - "8080:8080"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    command: --api.insecure=true  --providers.docker
  whoami:
    image: containous/whoami
    ports:
      - "82:80"
    labels:
      - "traefik.http.routers.whoami.rule=Host(`whoami.docker.localhost`)"
      #- "traefik.http.routers.whoami.rule=Host(`whoami.docker.localhost`) || PathPrefix(`/whoami/`)"
      #- "traefik.http.routers.whoami.rule=PathPrefix(`/whoami/`)"
```

* reverse-proxy是traefik服务, 提供了两个接口 http://127.0.0.1:81, http://127.0.0.1:8080

* --api.insecure=true开启api

* --providers.docker表示traefik使用docker接口找服务

* lables属于容器标签, traefik找到容器后，会读取标签，进行路由配置

* whoami是一个http服务 http://127.0.0.1:82

### 访问whoami

```bash
# 方法1: 直接访问whoami服务(此处是82端口)
http://127.0.0.1:82

# 方法2: 通过traefik代理访问whoami的http服务(此处是82端口)
# -H设置http请求header中的Host
# http://127.0.0.1:81访问traefik端口
curl -H Host:whoami.docker.localhost  http://127.0.0.1:81

# 方法3: 通过http前缀访问(此处是82端口)
# 打开whoami容器labels的第二行, 支持Host和PathPrefix两种方式
http://127.0.0.1:81/whoami/
```

### 代理规则

* 代理规则: Host

```bash
Host(`whoami.docker.localhost`)   # 发送http请求的Header中的Host改为whoami.docker.localhost
```

* 代理规则: url前缀

```angular2html
http://ip:port/whoami/     # 类似nginx的location
```


### 问题列表

* 问题1: traefik如何找到服务whoami?

--providers.docker指定了服务来源于docker, traefix会调用docker接口找服务，并解析labels获取匹配规则

* 问题2: host规则如何使用

有两种方式， 第一种，发送请求前修改header中的host参数; 第二种，使用域名访问


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)

[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)

[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)