+++
title = "golang编译-ldflags -H windowsgui病毒"
date = 2022-07-25T11:40:00+08:00
categories = ["golang"]
tags = ["golang"]
draft = false
summary = "使用walk为程序做了一个windows界面, cmd窗口需要隐藏. 结果exe程序被杀毒软件认为是病毒"
+++

### 介绍

    使用walk为程序做了一个windows界面, cmd窗口需要隐藏。使用了 go build -ldflags="-H windowsgui"

    结果exe程序被杀毒软件认为是病毒

### 为什么会认为是病毒？

    隐藏cmd窗口, 双击exe，可以直接启动，并且看不到任何窗口。这不就是病毒的喜欢干的事吗.

    所以有些杀毒软件, 干脆通杀, 使用了 "-H windowsgui" 直接被认为是病毒

    我们这些正常的需求该咋办呢？

### 解决办法

    在程序中，使用代码隐藏cmd窗口


```go
import "github.com/lxn/win"

//隐藏cmd窗口
win.ShowWindow(win.GetConsoleWindow(), win.SW_HIDE)
```

    缺点：启动时会有很短暂的黑框闪现，不过问题不大


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)