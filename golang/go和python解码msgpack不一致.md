### 描述

    有一个消息字典类型, 使用go进行编码, 传入消息中间件。同时有一个python程序监听, 监听到消息之后进行解码, 遇到了编码问题


### 环境

```
go使用:     github.com/vmihailenco/msgpack/v5 v5.3.4
python使用: msgpack==1.0.2
```


python解码

```python
# go编码的对象是个字典，value可能包含了数组，并且数组类型复杂
msgpack.unpackb(xxx)
```


错误提示

```
UnicodeDecodeError: 'utf-8' codec can't decode byte 0bx0 in position 0: invalid start byte
```

解决办法

```python
msgpack.unpackb(xxx, raw=True)

#raw=True  解析为python字节类型
#raw=False 解析为python字符串, 默认使用utf-8编码
```

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)