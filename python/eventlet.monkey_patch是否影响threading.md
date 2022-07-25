### 说明

evenlet.monkey_patch()默认会绿化thread，并未看到绿化threading，为什么实际中threading被绿化了，如何证明？ 测试代码如下

### 修改evenlet，加上标记语句

修改eventlet.green.thread.py的start_new_thread方法，加一条打印语句

```
def start_new_thread(function, args=(), kwargs=None):
    ...
    print("hello: ", locals())
    g = greenthread.spawn_n(__thread_body, function, args, kwargs)
    return get_ident(g)
```


### 测试代码

```
# -*- coding:utf-8 -*-
# from eventlet import patcher
# original_threading = patcher.original('threading')
import threading as original_threading
import time
import eventlet
eventlet.monkey_patch()


def thread1_run():
    while True:
        print("thread1:", time.time())
        time.sleep(1)


if __name__ == "__main__":
    thread1 = original_threading.Thread(target=thread1_run, name="thread1", daemon=True)
    print(type(thread1))
    thread1.start()

    e = eventlet.Event()
    e.wait()
```

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://doc.sxjy360.top/book/佛說大乘無量壽莊嚴清淨平等覺經(難字注音).pdf)
[净土大经科注2014-doc](http://doc.sxjy360.top/book/净土大经科注2014-doc.zip)
[此生必看的科学实验-水知道答案](http://v.youku.com/v_show/id_XMjgzMzcwNDk4OA)
[印光大师十念法（胡小林主讲第1集）](http://v.youku.com/v_show/id_XMzUwMzc4NzY4NA)

