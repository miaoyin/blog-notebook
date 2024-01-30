+++
title = "gin日志如何使用zap写日志"
date = 2022-07-25T11:40:00+08:00
categories = ["golang"]
tags = ["gin", "zap", "golang"]
draft = false
summary = "gin对外提供了io.Writer接口, 并且默认日志写入到控制台. 如何使用zap写入文件呢?"
+++

### 描述

    gin对外提供了io.Writer接口, 并且默认日志写入到控制台. 如何使用zap写入文件呢?

### 示例

```go
logger := zap.NewExample()
gin.DefaultWriter = &zapio.Writer{Log: logger, Level: zap.DebugLevel}
ga.Server = gin.Default()

//注意: 要在gin.Default之前, 修改才有效
```
