### 问题

pyserial访问windows中的串口，如何绿化?不阻塞协程? 效率最高? 绿化毫无疑问要借用tpool.execute.

代码该如何实现才最佳呢？

### 方法一: 直接recv

直接recv，会阻塞协程

缺点: 协程被阻塞，程序不可用

### 方法二: tpool+in_waiting判断

```
import serial
from eventlet import tpool
class SerialSocket(serial.Serial):
    def recv(self):
        # 读完
        if self.in_waiting:
            return tpool.execute(self.read, self.in_waiting)
        return data

def listen_message():
    ss = SerialSocket("com10")
    buffer = b''
    while True:
        data = ss.recv()
        if data is not None:
            buffer +=data
```

缺点: cpu利用率较高

### 方法三: tpool+in_waiting判断+sleep

```
import serial
from eventlet import tpool, sleep
class SerialSocket(serial.Serial):
    def recv(self):
        # 读完
        if self.in_waiting:
            return tpool.execute(self.read, self.in_waiting)
        return data

def listen_message():
    ss = SerialSocket("com10")
    buffer = b''
    while True:
        data = ss.recv()
        if data is not None:
            buffer +=data
        else:
            sleep(0.01)

```

缺点: 满足大部分需求, 对时间要求比较敏感的程序，还是不行，多了0.01秒

### 方法四(完美)：tpool+in_waiting判断+read(1)阻塞

利用serial的read()阻塞特性，加上tpool线程，最终线程阻塞转换成协程阻塞

```
import serial
from eventlet import tpool

class SerialSocket(serial.Serial):
    def recv(self):
        # 等待
        data = tpool.execute(self.read, 1)
        # 读完
        while self.in_waiting:
            data += tpool.execute(self.read, self.in_waiting)
        return data

def listen_message():
    ss = SerialSocket("com10")
    buffer = b''
    while True:
        g = evenlet.spawn(ss.recv)
        data = g.wait()
        buffer +=data
```

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://doc.sxjy360.top/book/佛說大乘無量壽莊嚴清淨平等覺經(難字注音).pdf)
[净土大经科注2014-doc](http://doc.sxjy360.top/book/净土大经科注2014-doc.zip)
[此生必看的科学实验-水知道答案](http://v.youku.com/v_show/id_XMjgzMzcwNDk4OA)
[印光大师十念法（胡小林主讲第1集）](http://v.youku.com/v_show/id_XMzUwMzc4NzY4NA)
