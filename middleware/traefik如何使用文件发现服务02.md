traefik如何使用文件发现服务


### 问题

traefik如何使用配置文件发现服务？ 

使用docker启动traefik, traefik使用配置文件, 并且服务也使用配置文件

通过docker-compose.yaml引用traefik.yml, config.yml


### docker-compose.yaml

```yaml
version: "3"
services:
  reverse-proxy:
    #image: traefik:v2.10
    image: traefik:latest
    ports:
      - "80:80"
      - "8080:8080"
    volumes:
      - ./traefik.yml:/etc/traefik/traefik.yml
      - ./config.yml:/etc/traefik/config.yml
  whoami:
    image: containous/whoami
    ports:
      - "5000:80"
```

### traefik配置文件traefik.yml

```yml
api:
    insecure: true
...
providers:
    file:
        filename: /etc/traefik/config.yml

```

### 服务发现配置config.yml


```yml
http:
    routers:
      my-router:
        rule: "Path(`/whoami/`)"
        service: whoami
    services:
      whoami:
        loadBalancer:
          servers:
            - url: "http://xxxx:5000/"
```


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)

[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)

[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)
