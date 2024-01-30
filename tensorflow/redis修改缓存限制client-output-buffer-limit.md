+++
title = "redis修改缓存限制client-output-buffer-limit"
date = 2022-07-25T12:19:00+08:00
summary = "celery异步调用，经常会传一些比较大的数据，调用比较多会经常出现连接被关闭"
categories = ["tensorflow"]
tags = ["tensorflow", "redis"]
draft = false
+++


# 描述

celery异步调用，经常会传一些比较大的数据，调用比较多会经常出现连接被关闭

# 错误内容

```
  File "/usr/local/lib/python3.6/dist-packages/celery/result.py", line 228, in get
    on_message=on_message,
  File "/usr/local/lib/python3.6/dist-packages/celery/backends/asynchronous.py", line 193, in wait_for_pending
    for _ in self._wait_for_pending(result, **kwargs):
  File "/usr/local/lib/python3.6/dist-packages/celery/backends/asynchronous.py", line 260, in _wait_for_pending
    on_interval=on_interval):
  File "/usr/local/lib/python3.6/dist-packages/celery/backends/asynchronous.py", line 56, in drain_events_until
    yield self.wait_for(p, wait, timeout=1)
  File "/usr/local/lib/python3.6/dist-packages/celery/backends/asynchronous.py", line 65, in wait_for
    wait(timeout=timeout)
  File "/usr/local/lib/python3.6/dist-packages/celery/backends/redis.py", line 127, in drain_events
    message = self._pubsub.get_message(timeout=timeout)
  File "/usr/local/lib/python3.6/dist-packages/redis/client.py", line 3565, in get_message
    response = self.parse_response(block=False, timeout=timeout)
  File "/usr/local/lib/python3.6/dist-packages/redis/client.py", line 3451, in parse_response
    if not block and not conn.can_read(timeout=timeout):
  File "/usr/local/lib/python3.6/dist-packages/redis/connection.py", line 729, in can_read
    return self._parser.can_read(timeout)
  File "/usr/local/lib/python3.6/dist-packages/redis/connection.py", line 313, in can_read
    return self._buffer and self._buffer.can_read(timeout)
  File "/usr/local/lib/python3.6/dist-packages/redis/connection.py", line 223, in can_read
    raise_on_timeout=False)
  File "/usr/local/lib/python3.6/dist-packages/redis/connection.py", line 193, in _read_from_socket
    raise ConnectionError(SERVER_CLOSED_CONNECTION_ERROR)
redis.exceptions.ConnectionError: Connection closed by server.
```

# redis日志/var/log/redis/redis-server.log

```
1163:M 27 Apr 17:12:17.163 # Client id=93 addr=172.17.0.3:41098 fd=15 name= age=70 idle=1 flags=N db=0 sub=4 psub=0 multi=-1 qbuf=0 qbuf-free=0 obl=87 oll=1 omem=8388648 events=rw cmd=subscribe scheduled to be closed ASAP for overcoming of output buffer limits.
```

# 修改配置参数解决

```
# /etc/redis/redis.conf
# client-output-buffer-limit <class> <hard limit> <soft limit> <soft seconds>
# 条件1 hard limit表示超过该数值, server直接关闭连接
# 条件2 soft limit表示超过该数值，持续soft seconds秒, server关闭连接

# 默认
client-output-buffer-limit pubsub 32mb 8mb 60

# 改为(0表示不限制)
client-output-buffer-limit pubsub 0 0 0
```
