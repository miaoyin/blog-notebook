+++
title = "deeplab错误Invalid argument  Loss is inf or nan Tensor had NaN values"
date = 2022-07-25T12:19:00+08:00
summary = "tensorflow deeplab训练到一定次数出现错误Invalid argument: Loss is inf or nan. : Tensor had NaN values"
categories = ["tensorflow"]
tags = ["deeplab", "tensorflow"]
draft = false
+++

# 问题描述

tensorflow deeplab训练到一定次数出现错误Invalid argument: Loss is inf or nan. : Tensor had NaN values

# 错误内容

```
I0423 08:55:47.280277 139646519609088 learning.py:507] global step 1990: loss = 0.3756 (1.255 sec/step)
I0423 08:55:51.296706 139618548692736 supervisor.py:1117] Saving checkpoint to path /app/data/model/model.ckpt
I0423 08:55:51.831594 139618531907328 supervisor.py:1050] Recording summary at step 1994.
I0423 08:55:59.190809 139618540300032 supervisor.py:1099] global_step/sec: 0.851667
I0423 08:55:59.217599 139646519609088 learning.py:507] global step 2000: loss = 0.3757 (1.182 sec/step)
I0423 08:56:07.141482 139646519609088 coordinator.py:224] Error reported to Coordinator: <class 'tensorflow.python.framework.errors_impl.InvalidArgumentError'>, 2 root error(s) found.
  (0) Invalid argument: Loss is inf or nan. : Tensor had NaN values
	 [[node CheckNumerics (defined at deeplab/train.py:398) ]]
  (1) Invalid argument: Loss is inf or nan. : Tensor had NaN values
	 [[node CheckNumerics (defined at deeplab/train.py:398) ]]
	 [[clone_5/gradients/clone_5/xception_65/middle_flow/block1/unit_13/xception_module/separable_conv1_depthwise/depthwise_grad/tuple/control_dependency_1/_26152]]
0 successful operations.
7 derived errors ignored.
```

# 原因

制作的数据集有问题，分类不清

# 备注

有人说是减小--base_learning_rate，--fine_tune_batch_norm=false 实际没有用，同样会出现上面问题。


[此生必看的科学实验-水知道答案](http://v.youku.com/v_show/id_XMjgzMzcwNDk4OA)
[印光大师十念法（胡小林主讲第1集）](http://v.youku.com/v_show/id_XMzUwMzc4NzY4NA)