+++
title = "protobuf序列化numpy"
date = 2022-07-25T12:12:00+08:00
summary = "protobuf处理不能直接处理numpy，需要先把numpy转为字节"
categories = ["python"]
tags = ["python", "protobuf", "numpy"]
draft = false
+++

### 说明

protobuf处理不能直接处理numpy，需要先把numpy转为字节


### numpy转字节

```
import numpy as np
from io import BytesIO
A = np.array([ 
      1, 2, 3, 4, 4,
      2, 3, 4, 5, 3,
      4, 5, 6, 7, 2,
      5, 6, 7, 8, 9,
      6, 7, 8, 9, 0 ]).reshape(5,5)

   
# numpy 转bytes
nda_bytes = BytesIO()
np.save(nda_bytes, A, allow_pickle=False)

# bytes转numpy
nda_bytes = BytesIO(nda_bytes.getvalue())
B = np.load(nda_bytes, allow_pickle=False)
print(np.array_equal(A, B))
```

### 定义protobuf message

ndarray.proto

```
syntax = "proto3";

message NDArray {
    bytes ndarray = 1;
}
```

### 使用

```
from io import BytesIO
import numpy as np
import ndarray_pb2   #上面ndarray.proto编译成python


def ndarray_to_proto(nda: np.ndarray) -> NDArray:
    """
    numpy转proto
    """
    nda_bytes = BytesIO()
    np.save(nda_bytes, nda, allow_pickle=False)
    return NDArray(ndarray=nda_bytes.getvalue())


def proto_to_ndarray(nda_proto: NDArray) -> np.ndarray:
    nda_bytes = BytesIO(nda_proto.ndarray)
    return np.load(nda_bytes, allow_pickle=False)



A = np.array([ 
      1, 2, 3, 4, 4,
      2, 3, 4, 5, 3,
      4, 5, 6, 7, 2,
      5, 6, 7, 8, 9,
      6, 7, 8, 9, 0 ]).reshape(5,5)
serialized_A = ndarray_to_proto(A)
deserialized_A = proto_to_ndarray(serialized_A)
assert np.array_equal(A, deserialized_A)

```

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)

