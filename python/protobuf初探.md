+++
title = "protobuf初探"
date = 2022-07-25T12:12:00+08:00
summary = "以前是通过grpc知道的protobuf，未尝试过，以为挺复杂，一直没怎么重视过。后来因为tensorflow的很多地方用到了protobuf，正好我又在研究消息传递，如何优化numpy传输。"
categories = ["python"]
tags = ["python", "protobuf"]
draft = false
+++


### 起因

以前是通过grpc知道的protobuf，未尝试过，以为挺复杂，一直没怎么重视过。
后来因为tensorflow的很多地方用到了protobuf，正好我又在研究消息传递，如何优化numpy传输。

### protobuf是什么

Protobuf是一种平台无关、语言无关、可扩展且轻便高效的序列化数据结构的协议。比xml, 更小，更快，更简单.


* github
https://github.com/protocolbuffers/protobuf

* 文档
https://developers.google.com/protocol-buffers

    
### 好不好用

* 优点

a. 语言无关、平台无关。(pickle等, 依赖语言)
b. 无依赖问题。(pickle等, 依赖关联引用)
c. 无安全漏洞. (pickle等, 有安全漏洞)
d. 体积更小, 性能好 (经过压缩，编码, 比json,xml等更小)
e. 简单易用. (算是简单易用)

* 缺点

a.需要事先定义,缺少灵活性 (从另一种角度理解，反而是优点。清楚的定义了数据格式。)


### 安装

* 环境
操作系统: fedora
开发语言: python3

* 安装编译器

yum install protobuf-compiler

* 安装api

pip install protobuf


* 定义message

person.proto

```
syntax="proto3";
package example;

message person {
    int32 id=1;
    string name = 2;
}

message all_person {
    repeated person Per = 1;
}
```

* 编译

protoc person.proto --python_out=.

* 序列化

```
import person_pb2

pers = person_pb2.all_person()
p1 = pers.Per.add()
p1.id=1
p1.name="xieyanke"

p2 = pers.Per.add()
p2.id=2
p2.name="pythoner"


#序列化
data = pers.SerializeToString()
print(data)

# 解析
target = person_pb2.all_person()
target.ParseFromString(data)
print(target)
```

### 测试

体积测试：根据自己的需要定义message, 对比其它方式的序列化结果。

### 总结

protobuf跨语言、无依赖、体积小、速度快。优势比较明显。需要先定义也并不算是缺点.
