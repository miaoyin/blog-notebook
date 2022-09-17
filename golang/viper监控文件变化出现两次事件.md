---
title: viper监控文件变化出现两次事件
description:
published: true
date: 2022-09-17T19:34:00
tags:
editor: markdown
dateCreated: 2022-09-17T19:34:00Z
---

# 问题描述

监控配置文件变化，如果内容发生了修改，需要及时加载. 使用viper监控文件变化发现了, 对文件修改一次，会出发两次事件.

# 示例代码

```go
viper.OnConfigChange(func(e fsnotify.Event) {
	if e.Op & fsnotify.Write != 0 {
		fmt.Println("Config file changed:", e.Name)
	}
})
viper.WatchConfig()
```

#  推测原因

* 检查viper的源码是否存在bug?

未发现异常

* 两次变化viper读到的内容返回值是否不一样? 

发现viper.AllKeys()返回值不一样, 一次为空, 一次正常.

* 换一个电脑试试?

发现了在公司办公电脑出现，家里电脑不会出现。

# 解决办法

```go
//使用AllKeys进行过滤
viper.OnConfigChange(func(e fsnotify.Event) {
	if e.Op & fsnotify.Write != 0 && viper.AllKeys()>0{
		fmt.Println("Config file changed:", e.Name)
	}
})
viper.WatchConfig()

```


# 总结

公司的办公电脑安装了文件加密软件。我估摸着是它搞了个什么鬼.


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)

[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)

[净土大经科注2014(mp3)](http://www.sxjy360.top/page-download/)

[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)







