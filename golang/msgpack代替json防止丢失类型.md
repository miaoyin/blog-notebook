+++
title = "msgpack代替json防止丢失类型"
date = 2022-07-25T12:03:00+08:00
categories = ["golang"]
tags = ["golang", "msgpack"]
draft = false
summary = "开发了一个通信协议, 协议对数据类型比较敏感, 根据不同数据数据类型使用不同格式。golang的数据类型能够满足需求。"
+++

### 描述

开发了一个通信协议, 协议对数据类型比较敏感, 根据不同数据数据类型使用不同格式。golang的数据类型能够满足需求。

然而，将协议转为http方式时，如果传入json，会发现数据类型不符合预期

例如，本来是应该int结果是float64; 本来应该是[]byte，结果是string

### 环境

http服务器: gin

开发语言: golang

### 解决方法探寻

什么传输协议会带上数据类型？

* json类型丢失

默认情况下gin的BindJSON，可将传入的参数绑定到具体的结构体，产生类型转换。然后，我的结果需要是动态的，不知道参数的具体内容。

* 自定义参数格式，将类型带上。例如 marchinery

```go
Arg struct{
    Type string
    Value interface{}
}
```

缺点：太麻烦

* 常用协议中选择

xml         可以用，不理想

yaml        不能解决问题

protobuf    不能解决问题

messagepack 有详细的数据类型，完美


### msgpack替代json

gin示例

```go
func SendXX(c *gin.Context){
    rawData, _ := c.GetRawData()
    var param interface{}
    msgpack.Unmarshal(rawData, &param)

	result := struct{
		Name string
	}{
		Name: "xxx",
	}
	resultData, _ := msgpack.Marshal(result)
	c.Data(200, "application/x-msgpack", resultData)
}
```

### 备注

* gin自带的msgpack版本是1.1.7不好用，不会自动进行类型转换。 最好改用  github.com/vmihailenco/msgpack/v5

* 替换gin中的部分接口就可以，不用全部改为msgpack


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)