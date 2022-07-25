
### 描述

celery+eventlet实现任务调用时出现RuntimeError: Second simultaneous read on fileno 14 detected.  Unless you really know what you're doing, make sure that only one greenthread can read any particular socket


### 模拟错误

网上找的一段代码，模拟出同样问题

```

def main():
    import eventlet
    httplib2 = eventlet.import_patched('httplib2')
    shared_resource = httplib2.Http()

    def get_url():
        resp, content = shared_resource.request("http://eventlet.net")
        return content

    p = eventlet.GreenPile()
    p.spawn(get_url)
    p.spawn(get_url)
    results = list(p)
    assert results[0] == results[1]


if __name__ == "__main__":
    main()
    
```

### 错误内容

```
Traceback (most recent call last):
  File "/root/miniconda3/envs/defect/lib/python3.7/site-packages/eventlet/hubs/poll.py", line 111, in wait
    listener.cb(fileno)
  File "/root/miniconda3/envs/defect/lib/python3.7/site-packages/eventlet/greenthread.py", line 221, in main
    result = function(*args, **kwargs)
  File "test.py", line 12, in get_url
    resp, content = shared_resource.request("http://eventlet.net")
  File "/root/miniconda3/envs/defect/lib/python3.7/site-packages/httplib2/__init__.py", line 1994, in request
    cachekey,
  File "/root/miniconda3/envs/defect/lib/python3.7/site-packages/httplib2/__init__.py", line 1651, in _request
    conn, request_uri, method, body, headers
  File "/root/miniconda3/envs/defect/lib/python3.7/site-packages/httplib2/__init__.py", line 1558, in _conn_request
    conn.request(method, request_uri, body, headers)
  File "/root/miniconda3/envs/defect/lib/python3.7/http/client.py", line 1252, in request
    self._send_request(method, url, body, headers, encode_chunked)
  File "/root/miniconda3/envs/defect/lib/python3.7/http/client.py", line 1298, in _send_request
    self.endheaders(body, encode_chunked=encode_chunked)
  File "/root/miniconda3/envs/defect/lib/python3.7/http/client.py", line 1247, in endheaders
    self._send_output(message_body, encode_chunked=encode_chunked)
  File "/root/miniconda3/envs/defect/lib/python3.7/http/client.py", line 1026, in _send_output
    self.send(msg)
  File "/root/miniconda3/envs/defect/lib/python3.7/http/client.py", line 987, in send
    self.sock.sendall(data)
  File "/root/miniconda3/envs/defect/lib/python3.7/site-packages/eventlet/greenio/base.py", line 403, in sendall
    tail = self.send(data, flags)
  File "/root/miniconda3/envs/defect/lib/python3.7/site-packages/eventlet/greenio/base.py", line 397, in send
    return self._send_loop(self.fd.send, data, flags)
  File "/root/miniconda3/envs/defect/lib/python3.7/site-packages/eventlet/greenio/base.py", line 392, in _send_loop
    timeout_exc=_timeout_exc)
  File "/root/miniconda3/envs/defect/lib/python3.7/site-packages/eventlet/greenio/base.py", line 210, in _trampoline
    mark_as_closed=self._mark_as_closed)
  File "/root/miniconda3/envs/defect/lib/python3.7/site-packages/eventlet/hubs/__init__.py", line 157, in trampoline
    listener = hub.add(hub.WRITE, fileno, current.switch, current.throw, mark_as_closed)
  File "/root/miniconda3/envs/defect/lib/python3.7/site-packages/eventlet/hubs/epolls.py", line 22, in add
    listener = hub.BaseHub.add(self, evtype, fileno, cb, tb, mac)
  File "/root/miniconda3/envs/defect/lib/python3.7/site-packages/eventlet/hubs/hub.py", line 181, in add
    evtype, fileno, evtype, cb, bucket[fileno]))
RuntimeError: Second simultaneous write on fileno 5 detected.  Unless you really know what you're doing, make sure that only one greenthread can write any particular socket.  Consider using a pools.Pool. If you do know what you're doing and want to disable this error, call eventlet.debug.hub_prevent_multiple_readers(False) - MY THREAD=<built-in method switch of GreenThread object at 0x7f25df161680>; THAT THREAD=FdListener('write', 5, <built-in method switch of GreenThread object at 0x7f25df161470>, <built-in method throw of GreenThread object at 0x7f25df161470>)
```

### 解决方法

猜测可能是没绿化完整，改成如下，果然就正常了。

```
def main():
    import eventlet
#     httplib2 = eventlet.import_patched('httplib2')
    import http2
    eventlet.monkey_patch()
    shared_resource = httplib2.Http()

    def get_url():
        resp, content = shared_resource.request("http://eventlet.net")
        return content

    p = eventlet.GreenPile()
    p.spawn(get_url)
    p.spawn(get_url)
    results = list(p)
    assert results[0] == results[1]


if __name__ == "__main__":
    main()
```

### 结论

* 局部import的模块，可能使用了未被绿化的模块，导出eventlet出现问题

* 延迟加载的模块配合eventlet也会出现同样问题


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://doc.sxjy360.top/book/佛說大乘無量壽莊嚴清淨平等覺經(難字注音).pdf)
[净土大经科注2014-doc](http://doc.sxjy360.top/book/净土大经科注2014-doc.zip)
[此生必看的科学实验-水知道答案](http://v.youku.com/v_show/id_XMjgzMzcwNDk4OA)
[印光大师十念法（胡小林主讲第1集）](http://v.youku.com/v_show/id_XMzUwMzc4NzY4NA)

