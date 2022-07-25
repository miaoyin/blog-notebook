### 问题描述

flask提供http接口调用celery任务, 并且flask使用eventlet方式运行

而celery采用redis作为backend

运行一段时间后发现redis连接数超过1000以上

celery竟然未释放释放redis连接

为什么?

### 代码片段

调用方式很正常，并没有特殊地方

```python
def send_task(self, name, timeout=60, **kwargs):
    t = self.send_task(name, **kwargs)
    return t.get(timeout=timeout)
```

### 问题探索

1.修改celery连接池等参数，然后并没有什么用

2.搜索资料，基本没有这种场景的问题

3.逐行调试celery源码，发现了问题所在

### 问题原因

celery的backend被缓存在threading.local()中, 而这个地方的threading被绿化了，实际上用的是eventlet.corolocal.local

此处的local缓存效果失效，所以每次都是新建backend连接

### 解决方法

自定义celery, 将backend缓存在属性中

```
class XXCelery(Celery):

    def init_app(self):
        self._my_backend = None

    def _get_backend(self):
        """
        修复celery应用 bug
        """
        if self._my_backend is None:
            self._my_backend = super(XXCelery, self)._get_backend()
        return self._my_backend
```

### 问题延申

1. eventlet.monkey_patch()应该只绿化thread了, celery中的threading.local在什么时候被绿化?

2. 每次新建的backend没被回收吗, 难道不自动释放redis连接?

问题已解决, 还有几个疑问没弄清, 节约发量，不想了.

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://doc.sxjy360.top/book/佛說大乘無量壽莊嚴清淨平等覺經(難字注音).pdf)
[净土大经科注2014-doc](http://doc.sxjy360.top/book/净土大经科注2014-doc.zip)
[此生必看的科学实验-水知道答案](http://v.youku.com/v_show/id_XMjgzMzcwNDk4OA)
[印光大师十念法（胡小林主讲第1集）](http://v.youku.com/v_show/id_XMzUwMzc4NzY4NA)