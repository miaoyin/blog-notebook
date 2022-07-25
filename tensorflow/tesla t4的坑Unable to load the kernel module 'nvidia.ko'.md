### 说明

安nvidia tesla T4显卡遇到的坑, 在ubuntu16.04上安装t4会遇到下面错误


### 错误内容

```
   make[1]: Leaving directory '/usr/src/linux-headers-4.4.0-142-generic'
-> done.
-> Kernel module compilation complete.
ERROR: Unable to load the kernel module 'nvidia.ko'.  This happens most frequently when this kernel module was built against the wrong or improperly configured kernel sources, with a version of gcc that differs from the one used to build the target kernel, or if another driver, such as nouveau, is present and prevents the NVIDIA kernel module from obtaining ownership of the NVIDIA GPU(s), or no NVIDIA GPU installed in this system is supported by this NVIDIA Linux graphics driver release.
```

### 解决方法

t4不支持普通服务器，更换成刀片服务器

### 补充

* 如果普通主机操作系统是win10，插上t4，安装驱动正常。

* 安装nvidia 2080Ti 驱动，如果忘记插显卡电源线，会提示同样错误


[此生必看的科学实验-水知道答案](http://v.youku.com/v_show/id_XMjgzMzcwNDk4OA)
[印光大师十念法（胡小林主讲第1集）](http://v.youku.com/v_show/id_XMzUwMzc4NzY4NA)
