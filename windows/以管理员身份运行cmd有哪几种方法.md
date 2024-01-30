+++
title = "以管理员身份运行cmd有哪几种方法"
date = 2022-07-25T12:21:00+08:00
summary = "除了右键以管理员身份运行，还有哪几种方法，能够以管理员身份启动cmd"
categories = ["windows"]
tags = ["windows", "cmd", "powershell"]
draft = false
+++


### 描述
    
    除了右键"以管理员身份运行"，还有哪几种方法，能够以管理员身份启动cmd

### 方式一：右键"以管理员方式运行"

### 方式二：powershell命令

```shell
Start-Process cmd -Verb runas
Start-Process powershell -Verb runas
```

### 方式三: 第三方工具

http://code.kliu.org/misc/elevate/

### 方式四：编程

在编译程序时，设置启动时请求权限


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)