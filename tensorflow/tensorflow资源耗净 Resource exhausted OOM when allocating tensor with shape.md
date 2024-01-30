+++
title = "tensorflow资源耗净 Resource exhausted OOM when allocating tensor with shape"
date = 2022-07-25T12:19:00+08:00
summary = "tensorflow跑训练集经常会遇到错误Resource exhausted: OOM when allocating tensor with shape[64,33,33,2048]"
categories = ["tensorflow"]
tags = ["tensorflow"]
draft = false
+++


### 说明

tensorflow跑训练集经常会遇到错误Resource exhausted: OOM when allocating tensor with shape[64,33,33,2048]

### 错误内容

```
tensorflow.python.framework.errors_impl.ResourceExhaustedError: 2 root error(s) found.
  (0) Resource exhausted: OOM when allocating tensor with shape[64,33,33,2048] and type float on /job:localhost/replica:0/task:0/device:GPU:0 by allocator GPU_0_bfc
	 [[node SecondStageBoxPredictor_1/ResizeBilinear (defined at /app/models/research/object_detection/predictors/heads/mask_head.py:149) ]]
Hint: If you want to see a list of allocated tensors when OOM happens, add report_tensor_allocations_upon_oom to RunOptions for current allocation info.

	 [[total_loss/_7771]]
Hint: If you want to see a list of allocated tensors when OOM happens, add report_tensor_allocations_upon_oom to RunOptions for current allocation info.

  (1) Resource exhausted: OOM when allocating tensor with shape[64,33,33,2048] and type float on /job:localhost/replica:0/task:0/device:GPU:0 by allocator GPU_0_bfc
	 [[node SecondStageBoxPredictor_1/ResizeBilinear (defined at /app/models/research/object_detection/predictors/heads/mask_head.py:149) ]]
Hint: If you want to see a list of allocated tensors when OOM happens, add report_tensor_allocations_upon_oom to RunOptions for current allocation info.

0 successful operations.
0 derived errors ignored.

Errors may have originated from an input operation.
Input Source operations connected to node SecondStageBoxPredictor_1/ResizeBilinear:
 SecondStageFeatureExtractor/resnet_v1_101/block4/unit_3/bottleneck_v1/Relu (defined at /app/models/research/slim/nets/resnet_v1.py:136)

Input Source operations connected to node SecondStageBoxPredictor_1/ResizeBilinear:
 SecondStageFeatureExtractor/resnet_v1_101/block4/unit_3/bottleneck_v1/Relu (defined at /app/models/research/slim/nets/resnet_v1.py:136)
```

### 原因

tensorflow在为张量shape[64,33,33,2048]分配gpu内存是发现资源不够。

假如数据类型是int8, 该张量需要的内存大小64*33*33*2048*1B=142737408B = 142.7MB

### 解决方法

1. 降低图片质量
2. batch_size改成1
3. 改用大内存的显卡
4. 增加显卡, 并行训练

[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)