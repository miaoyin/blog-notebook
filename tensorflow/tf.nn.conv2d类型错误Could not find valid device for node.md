+++
title = "tf.nn.conv2d类型错误Could not find valid device for node"
date = 2022-07-25T12:19:00+08:00
summary = "tf.nn.conv2d卷积要求input的类型是half, bfloat16, float32, float64。"
categories = ["tensorflow"]
tags = ["tensorflow", "tesla"]
draft = false
+++

## 描述

tf.nn.conv2d类型错误Could not find valid device for node

tf.nn.conv2d卷积要求input的类型是half, bfloat16, float32, float64。

读取图片之后类型是unit8，需要转换

# 错误内容

```
NotFoundError: Could not find valid device for node.
Node:{{node Conv2D}}
All kernels registered for op Conv2D :
  device='XLA_GPU'; T in [DT_FLOAT, DT_DOUBLE, DT_INT32, DT_BFLOAT16, DT_HALF]
  device='XLA_CPU'; T in [DT_FLOAT, DT_DOUBLE, DT_INT32, DT_BFLOAT16, DT_HALF]
  device='XLA_CPU_JIT'; T in [DT_FLOAT, DT_DOUBLE, DT_INT32, DT_BFLOAT16, DT_HALF]
  device='XLA_GPU_JIT'; T in [DT_FLOAT, DT_DOUBLE, DT_INT32, DT_BFLOAT16, DT_HALF]
  device='GPU'; T in [DT_INT32]
  device='GPU'; T in [DT_DOUBLE]
  device='GPU'; T in [DT_FLOAT]
  device='GPU'; T in [DT_HALF]
  device='CPU'; T in [DT_INT32]
  device='CPU'; T in [DT_DOUBLE]
  device='CPU'; T in [DT_FLOAT]
  device='CPU'; T in [DT_HALF]
 [Op:Conv2D]
```

# 转换方法

```
input = tf.cast(input, tf.float32)
```

[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)