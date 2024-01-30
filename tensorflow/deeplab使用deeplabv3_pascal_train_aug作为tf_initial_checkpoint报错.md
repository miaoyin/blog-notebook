+++
title = "deeplab使用deeplabv3_pascal_train_aug作为tf_initial_checkpoint报错"
date = 2022-07-25T12:19:00+08:00
summary = "tensorflow deeplab训练自己的数据集，指定初始化模型，会出现错误"
categories = ["tensorflow"]
tags = ["deeplab", "tensorflow"]
draft = false
+++

# 问题描述

tensorflow deeplab训练自己的数据集，指定初始化模型，会出现错误
ValueError: Total size of new array must be unchanged for logits/semantic/weights lh_shape: [(1, 1, 256, 21)], rh_shape: [(1, 1, 256, 3)]

# 训练命令

```
python deeplab/train.py \
  --logtostderr \
  --dataset=lingxing \
  --train_split="trainval" \
  --model_variant="xception_65" \
  --atrous_rates=6 \
  --atrous_rates=12 \
  --atrous_rates=18 \
  --output_stride=16 \
  --decoder_output_stride=4 \
  --train_crop_size="1024,1024" \
  --train_batch_size=1 \
  --training_number_of_steps=150000 \
  --fine_tune_batch_norm=false \
  --tf_initial_checkpoint=/root/wyq/xxx/init_models/deeplabv3_pascal_train_aug/model.ckpt \
  --train_logdir=/root/xxx/model \
  --dataset_dir=/root/wyq/xxx/data \
  --num_clones=1 
```

# 错误内容

```
I0424 14:32:23.866076 140348105623296 train_utils.py:207] Initializing model from path: /root/wyq/xxxx/init_models/deeplabv3_pascal_train_aug/model.ckpt
Traceback (most recent call last):
  File "deeplab/train.py", line 466, in <module>
    tf.app.run()
  File "/root/miniconda3/envs/solar-gpu/lib/python3.6/site-packages/tensorflow/python/platform/app.py", line 40, in run
    _run(main=main, argv=argv, flags_parser=_parse_flags_tolerate_undef)
  File "/root/miniconda3/envs/solar-gpu/lib/python3.6/site-packages/absl/app.py", line 299, in run
    _run_main(main, args)
  File "/root/miniconda3/envs/solar-gpu/lib/python3.6/site-packages/absl/app.py", line 250, in _run_main
    sys.exit(main(argv))
  File "deeplab/train.py", line 446, in main
    ignore_missing_vars=True)
  File "/root/wyq/models/research/deeplab/utils/train_utils.py", line 221, in get_model_init_fn
    ignore_missing_vars=ignore_missing_vars)
  File "/root/miniconda3/envs/solar-gpu/lib/python3.6/site-packages/tensorflow/contrib/framework/python/ops/variables.py", line 694, in assign_from_checkpoint
    (ckpt_name, str(ckpt_value.shape), str(var.get_shape())))
ValueError: Total size of new array must be unchanged for logits/semantic/weights lh_shape: [(1, 1, 256, 21)], rh_shape: [(1, 1, 256, 3)]
```

# 错误原因

我的数据集类别是3, deeplabv3_pascal_train_aug模型的类别是21

# 解决办法

```
--initialize_last_layer=False
```


[此生必看的科学实验-水知道答案](http://v.youku.com/v_show/id_XMjgzMzcwNDk4OA)
[印光大师十念法（胡小林主讲第1集）](http://v.youku.com/v_show/id_XMzUwMzc4NzY4NA)