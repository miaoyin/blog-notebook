+++
title = "eventlet在ubuntu上出现OSError protocol not found"
date = 2022-07-25T12:12:00+08:00
summary = "tensorflow的nvidia docker镜像使用ubuntu16.04, ubuntu是精简之后的，有些包可能没有。在上面运行eventlet会出现下面问题"
categories = ["python"]
tags = ["python", "eventlet"]
draft = false
+++

### 描述

tensorflow的nvidia docker镜像使用ubuntu16.04, ubuntu是精简之后的，有些包可能没有。在上面运行eventlet会出现下面问题


### 错误内容

```

Traceback (most recent call last):
  File "/app/defect-client/defect_client/cmd/wafer-worker.py", line 14, in <module>
    import eventlet
  File "/usr/local/lib/python3.6/dist-packages/eventlet/__init__.py", line 10, in <module>
    from eventlet import convenience
  File "/usr/local/lib/python3.6/dist-packages/eventlet/convenience.py", line 7, in <module>
    from eventlet.green import socket
  File "/usr/local/lib/python3.6/dist-packages/eventlet/green/socket.py", line 21, in <module>
    from eventlet.support import greendns
  File "/usr/local/lib/python3.6/dist-packages/eventlet/support/greendns.py", line 69, in <module>
    setattr(dns.rdtypes.IN, pkg, import_patched('dns.rdtypes.IN.' + pkg))
  File "/usr/local/lib/python3.6/dist-packages/eventlet/support/greendns.py", line 59, in import_patched
    return patcher.import_patched(module_name, **modules)
  File "/usr/local/lib/python3.6/dist-packages/eventlet/patcher.py", line 126, in import_patched
    *additional_modules + tuple(kw_additional_modules.items()))
  File "/usr/local/lib/python3.6/dist-packages/eventlet/patcher.py", line 100, in inject
    module = __import__(module_name, {}, {}, module_name.split('.')[:-1])
  File "/usr/local/lib/python3.6/dist-packages/dns/rdtypes/IN/WKS.py", line 25, in <module>
    _proto_tcp = socket.getprotobyname('tcp')
OSError: protocol not found
```

### 解决办法

```
apt-get -o Dpkg::Options::="--force-confmiss" install --reinstall netbase
```

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)
