+++
title = "wikijs使用docker安装"
date = 2022-07-25T11:40:00+08:00
categories = ["docker"]
tags = ["wikijs", "docker", "docker-compose"]
draft = false
summary = "wikijs文档有docker-compose安装说明, 实际中会遇到几个问题"
+++

### 描述

wikijs文档有docker-compose安装说明, 实际中会遇到几个问题

### wikij官网

https://docs.requarks.io/install/docker

### 遇到问题

1. wikijs上传之后的问题如何备份?  需要将本地目录挂入docker, 并在wikijs存储中配置为上传路径.

2. docker volume如何备份与还原?  需要借助busybox镜像。如果直接从/var/lib/docker/volume目录拷贝会遇到很多问题.

### 修改之后的docker-compose.yaml

```yaml
version: "3"
services:

  wikidb:
    image: postgres:11-alpine
    environment:
      POSTGRES_DB: wiki
      POSTGRES_PASSWORD: wikijsrocks
      POSTGRES_USER: wikijs
    logging:
      driver: "none"
    restart: unless-stopped
    volumes:
      - db-data:/var/lib/postgresql/data

  wiki:
    image: ghcr.io/requarks/wiki:2
    depends_on:
      - wikidb
    environment:
      DB_TYPE: postgres
      DB_HOST: wikidb
      DB_PORT: 5432
      DB_USER: wikijs
      DB_PASS: wikijsrocks
      DB_NAME: wiki
    restart: unless-stopped
    volumes:
      - ./backup:/opt/backup
    ports:
      - "80:3000"

volumes:
  db-data:
```

### 备份与还原

```shell
//备份
docker run --rm -it -v ~/volume-backup:/backup -v /var/lib/docker:/docker busybox tar cfz /backup/volume.tgz /docker/volumes/wikijs_db-data
//还原
docker run --rm -it -v /var/lib/docker:/docker -v ~/volume-backup/docker/volumes:/volume-backup busybox cp -rp /volume-backup/wikijs_db-data /docker/volumes
```

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)

[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)

[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)

[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)