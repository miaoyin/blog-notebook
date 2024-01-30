+++
title = "gorm模型定义原理借鉴分析"
date = 2022-07-25T11:44:00+08:00
categories = ["golang"]
tags = ["golang", "gorm"]
draft = false
summary = "python有元类概念，在定义db模型时，相当方便，极大简化代码. go中没有元类概念, gorm有模型定义，看看它怎么实现，能否借鉴"
+++

### 说明

python有元类概念，在定义db模型时，相当方便，极大简化代码

go中没有元类概念, gorm有模型定义，看看它怎么实现，能否借鉴

### gorm原理

1. gorm运用了结构体标签，通过reflect获取标签内容，这是基本原理，这里不做介绍。

2. 关注它如何运用这些特性，借鉴使用

3. 直接分析源码太复杂，绕的路径太多。基于gorm模型定义最核心的代码，写一个小例子，展示gorm的用法，这也是我需要借鉴的地方

4. gorm所有的接口db.Create, db.Model,...最终都是调用schema.Parse


```go
package main

import (
	"fmt"
	"gorm.io/gorm"
	"gorm.io/gorm/schema"
	"reflect"
	"sync"
)

type Product struct {
	gorm.Model
	Code  string
	Price uint
}

func main() {
	product, _ := schema.Parse(&Product{}, &sync.Map{}, schema.NamingStrategy{})
	fmt.Println(product)
	fmt.Println(reflect.TypeOf(product))
}

//打印
//main.Product
//*schema.Schema
```

## 总结

1. 自定义的model，最终都被gorm转为Schema类型

2. model仅仅提供字段、标签信息

3. gorm并且实现了Schema的String() string方法，让打印看起来是Product

4. gorm的模型定义，比较encoding/json简单，比较适合借鉴使用


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)
