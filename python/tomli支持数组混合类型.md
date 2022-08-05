
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





