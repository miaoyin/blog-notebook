+++
title = "全局gin对象如何在子模块中修改Logger"
date = 2022-12-11T17:25:00+08:00
categories = ["golang"]
tags = ["golang", "gin"]
draft = false
summary = "gin是通过中间件写日志, 日志对象是中间件的闭包变量。在use加载中间件完成之后，无法修改日志对象。 该如何办呢？"
+++

### 说明

gin是通过中间件写日志, 日志对象是中间件的闭包变量。在use加载中间件完成之后，无法修改日志对象。 该如何办呢？

### 一般设置方法

以下是设置gin日志的方法

```go
#方法一
gin.DefaultWriter = xxx
gin.DefaultErrorWriter = xxx 
app := gin.Default()
app.Use(xxx)

#方法二
app := gin.Default()
app.Use(gin.LoggerWithWriter(xxx))
```

### 当情况发生变化

1. app := gin.Default()被放在了顶层模块，并且加载了默认中间件

2. 程序真正的log在子模块中初始化

3. 在子模块中如何修改gin的日志？

### 解决办法

借助zapio.Writer对象，通过修改它的Log属性实现

```go
# 顶层模块gin.go
gin.DefaultWriter =&zapio.Writer{Log:zap.L()}
gin.DefaultErrorWriter = gin.DefaultWriter
app := gin.Default()
app.use(xxx)


#子模块xxx/sim/http.go
if gl, ok := gin.DefaultWriter.(*zapio.Writer);ok{
    gl.Log = gs.log.Desugar()   #修改Log属性
}

```


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)
