+++
title = "eventlet模块中select出现ValueError错误"
date = 2022-07-25T12:12:00+08:00
summary = "如果socket句柄是-1, select.select()在判断句柄状态时会报错 ValueError: file descriptor cannot be a negative integer"
categories = ["python"]
tags = ["python", "eventlet"]
draft = false
+++

### 问题说明

如果socket句柄是-1, select.select()在判断句柄状态时会报错 ValueError: file descriptor cannot be a negative integer (-1)

而eventlet的select模块没有处理这种异常

### 出现环境

windows、python3.8、eventlet 0.26 在使用eventlet开发socket程序时, 如果客户端强制中断连接，会出现如下错误

### 错误描述

```
exception: [WinError 10054] 远程主机强迫关闭了一个现有的连接。
...
ValueError: file descriptor cannot be a negative integer (-1)
```

### 解决办法

修改文件eventlet\hubs\selects.py，然后把eventlet重新打包

```
    def wait(self, seconds=None):
        ....
        try:
            r, w, er = select.select(reader_fds, writer_fds, all_fds, seconds)
        # 捕捉异常并移除句柄-1
        except ValueError as e:
            self.remove_descriptor(-1)
            return
        except select.error as e:
            if support.get_errno(e) == errno.EINTR:
                return
            elif support.get_errno(e) in BAD_SOCK:
                self._remove_bad_fds()
                return
            else:
                raise
```

### 总结

1.很奇怪eventlet怎么没有修复这种兼容性错误?

2.模拟错误的方法  select.select([-1], [], [], 0)

3. socket中断句柄怎么变成了-1，非法的句柄-1又是如何进入eventlet的?


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)


