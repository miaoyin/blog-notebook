+++
title = "tomli支持数组混合类型"
date = 2022-08-05T13:58:00+08:00
summary = "使用python的toml包解析toml配置文件。配置文件中使用了混合类型数组，结果程序报错。"
categories = ["python"]
tags = ["python", "toml"]
draft = false
+++

# 起因
    
    使用python的toml包解析toml配置文件。配置文件中使用了混合类型数组，结果程序报错。

# 原因与解决方法

1. toml0.5规范不支持数组混合类型, toml1.0规范才支持数组混合类型。

2. python的toml解析包有多种，toml包不支持1.0，所以不支持混合类型

3. 建议使用rtoml, tomli等

# 混合示例

```
a=[1, 2.1]
b=["a", ["b", "c"]]
c=["a", {"b": 1}]
```

# rtoml用法

```
obj = rtoml.load("""
a=[1, 2.1]
""")
print(obj)
```


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)


