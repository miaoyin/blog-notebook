
### 描述
    docker-compose方式启动nats-server。nats的镜像非常小, docker-cpomse进行管理。下面是配置方式

### docker-compose.yaml配置 

```yaml
version: "2"
services:
   nats-server:
       #image: provide/nats-server:latest
       image: nats:latest
       volumes:
           - ./nats-server.conf:/nats-server.conf
       ports:
           - 4222:4222
```

### 配置文件nats-server.conf

```conf
# Client port of 4222 on all interfaces
port: 4222

# HTTP monitoring port
monitor_port: 8222

# This is for clustering multiple servers together.
cluster {

  # Route connections to be received on any interface on port 6222
  port: 6222

  # Routes are protected, so need to use them with --routes flag
  # e.g. --routes=nats-route://ruser:T0pS3cr3t@otherdockerhost:6222
  authorization {
    user: ruser
    password: T0pS3cr3t
    timeout: 2
  }

  # Routes are actively solicited and connected to from this server.
  # This Docker image has none by default, but you can pass a
  # flag to the gnatsd docker image to create one to an existing server.
  routes = []
}
```

### 启动

docker-compose -f docker-compose.yaml up/restart/start/stop

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)
