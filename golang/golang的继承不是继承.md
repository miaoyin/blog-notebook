+++
title = "golang的继承不是继承"
date = 2022-07-25T11:40:00+08:00
categories = ["golang"]
tags = ["golang"]
draft = false
summary = "struct嵌套，内层struct方法访问同名的属性，这个属性是谁的？"
+++

### 问题

struct嵌套，内层struct方法访问同名的属性，这个属性是谁的？

### 示例

```go
package main

import (
	"fmt"
)

type ProductA struct{
	Name string
}

func (p *ProductA) PrintName(){
	fmt.Println("a:", p.Name)
}

type ProductB struct{
	ProductA
	Name string
	Level string
}

func main() {
    b := ProductB{
		Name: "name-b",
		Level: "level",
	}
	b.PrintName()
}
// 仍然是ProductA的
//打印 a:
```

### 总结

1. go没有继承，只有组合
2. 只是提供了类似继承的便捷访问方式，不要被所谓的”继承“误导

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)
