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
