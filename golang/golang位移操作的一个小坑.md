+++
title = "golang位移操作的一个小坑"
date = 2022-07-25T11:44:00+08:00
categories = ["golang"]
tags = ["golang"]
draft = false
summary = "现有int16类型的整数，将它转为两个字节存放。在还原时，一不小心会就被坑了。"
+++

###  问题描述

    现有int16类型的整数，将它转为两个字节存放。在还原时，一不小心会就被坑了。


### 错误用法

```
length := int(keyBytes[cursor] + keyBytes[cursor+1] <<8)
```

### 正确用法

```
keyBytes := []byte{ 16, 1}
length := int(uint16(keyBytes[cursor]) + uint16(keyBytes[cursor+1]) <<8)
```

### 错误原因

字节byte位移时类型不会自动变化, 先要转换类型再位移。


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)
