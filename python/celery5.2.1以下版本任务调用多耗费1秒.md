+++
title = "celery5.2.1以下版本任务调用多耗费1秒"
date = 2022-07-25T12:12:00+08:00
summary = "5.0.5版本celery存在一个缺陷, 调用任务耗时多用1秒"
categories = ["python"]
tags = ["python", "celery"]
draft = false
+++


### 问题

    5.0.5版本celery存在一个缺陷, 调用任务耗时多用1秒

    这个问题在5.2.0, 5.1.2同样存在

    没理解为什么到5.2.1版本才解决，甚至一度把celery排除python技术栈

### 环境描述


```
python3.6
celery5.0.5
windows 32位
```


### 调用celery任务代码摘要

```python
# 1.send_task返回AsyncResult
# 2.AsyncResult的get()等待返回结果
# 3.get()会比真实多耗费1秒，并且每次请求都会出现

from celery import Celery
class xxxCelery(Celery):
    def call_xxx(self, name, timeout=120, **kwargs):
        LOG.info("send_task: %s" % locals())
        start = time.time()
        r = self.send_task(name, **kwargs)
        g = eventlet.spawn(r.get, timeout=timeout)
        result = g.wait()
        print("cost: %s" % time.time()-start)
        return
```

### 两个版本比对

分析celery源码之后，可以知道问题在drain_events()内部, 比较5.2.0和5.2.1版本

```python
#celery/backends/asynchronuse.py

class greenletDrainer(Drainer):
    ...
    def run(self):
        self._started.set()
        while not self._stopped.is_set():
            try:
                self.result_consumer.drain_events(timeout=1)
                # 新增了两句, 估摸着是这个问题
                self._send_drain_complete_event()
                self._create_drain_complete_event()
            except socket.timeout:
                pass
        self._shutdown.set()
```


### 解决办法

    celery升级到5.2.1

    python要升级到3.7以上版本(celery要求python3.7以上版本)

### 解决效果

    耗时从1000多ms变成了30ms

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)