### 说明

eventlet是一个必备工具，经常用，绿化原理有点忘记了，重新复习一遍.


### 三个主要问题

1. 绿化的原理是什么?

2. 绿化怎么管理？

3. 绿化怎么引入？

### 绿化原理

利用select/epolls/kqueue等操作系统提供的非阻塞操作，将阻塞改为非阻塞.

### 引用管理

eventlet在import之后，将模块中的属性绿化.

用一小段代码来查看看
```
import sys
import eventlet
# eventlet.monkey_patch()

httplib2 = eventlet.import_patched('httplib2')
print(httplib2)
print(httplib2.socket)

print("================")
for k,v in sys.modules.items():
    if "socket" in k:
        print(k, v)


# 打印内容
<module 'httplib2' from 'D:\\workspace\\venv\\xxx\\lib\\site-packages\\httplib2\\__init__.py'>
<module 'eventlet.green.socket' from 'D:\\workspace\\venv\\xxx\\lib\\site-packages\\eventlet\\green\\socket.py'>
================
_socket <module '_socket' from 'c:\\python\\python36\\DLLs\\_socket.pyd'>
socket <module 'socket' from 'c:\\python\\python36\\lib\\socket.py'>
__original_module_socket <module 'socket' from 'c:\\python\\python36\\lib\\socket.py'>

# 说明了什么？
绿化只是替换httplib2模块中的引用属性socket。并未改变sys.modules中的属性
```

### 引入绿化方法一：直接import

```
from eventlet.green import socket
```

### 引入绿化方法二：eventlet.import_patch

1.好处: 能绿化模块内部的系统模块
2.只能绿化os, select, socket, thread, time, psycopy, MySQLdb, bultines, subprocess
3.如果是import threading，不会自动绿化, 需要from eventlet.green import threading
4. 不会修改sys.modules

```
import eventlet
httplib2 = eventlet.import_patched('httplib2')
print(httplib2)
print(httplib2.socket)

# 打印
<module 'httplib2' from 'D:\\workspace\\venv\\autumn-secs\\lib\\site-packages\\httplib2\\__init__.py'>
<module 'eventlet.green.socket' from 'D:\\workspace\\venv\\autumn-secs\\lib\\site-packages\\eventlet\\green\\socket.py'>
```

### 引入绿化方法三：eventlet.monkey_patch

1.与import_patch不同，会直接修改sys.modules
2.但是也只能绿化os,select等等几个模块，其它模块需要用直接引用绿化

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://doc.sxjy360.top/book/佛說大乘無量壽莊嚴清淨平等覺經(難字注音).pdf)
[净土大经科注2014-doc](http://doc.sxjy360.top/book/净土大经科注2014-doc.zip)
[此生必看的科学实验-水知道答案](http://v.youku.com/v_show/id_XMjgzMzcwNDk4OA)
[印光大师十念法（胡小林主讲第1集）](http://v.youku.com/v_show/id_XMzUwMzc4NzY4NA)

