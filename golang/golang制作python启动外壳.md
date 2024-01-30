+++
title = "golang制作python启动外壳"
date = 2022-07-25T11:44:00+08:00
categories = ["golang"]
tags = ["golang", "windows", "pyinstaller"]
draft = false
summary = "pyhton程序在windows上运行, 如何双击启动？"
+++

### 描述

pyhton程序在windows上运行, 如何双击启动？

用.bat脚本，挺low

用pyinstaller编译exe，也不大好用

不如用golang写一个启动外壳

### 示例

xxx.go

```golang

package main

import (
	"fmt"
	"os"
	"os/exec"
	"path/filepath"
	"syscall"
)

func main() {
    // 从环境变量取路径
	path_home := os.Getenv("AUTUMN_HOME")

	filename := os.Args[0]
	filename_all := filepath.Base(filename)
	filename_suffix := filepath.Ext(filename)
	stem_name := filename_all[0:len(filename_all) - len(filename_suffix)]

	python_path := path_home + "./core/python.exe"
	config_file := "etc/xxx/"+stem_name+".conf"
	cmd_str := python_path + " -m autumn.cmd.xxx -c " + config_file

	fmt.Println(cmd_str)
	command := exec.Command("cmd","/c", "start "+ cmd_str)
	command.SysProcAttr = &syscall.SysProcAttr{HideWindow: true}
	command.Start()
}
```

### 注意

注意，将go输出结果设置成32位

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://doc.sxjy360.top/book/佛說大乘無量壽莊嚴清淨平等覺經(難字注音).pdf)
[净土大经科注2014-doc](http://doc.sxjy360.top/book/净土大经科注2014-doc.zip)
[此生必看的科学实验-水知道答案](http://v.youku.com/v_show/id_XMjgzMzcwNDk4OA)
[印光大师十念法（胡小林主讲第1集）](http://v.youku.com/v_show/id_XMzUwMzc4NzY4NA)
