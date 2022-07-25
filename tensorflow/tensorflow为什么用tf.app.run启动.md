
### tf.app.run的原理以及为什么?

1. tf的命令行参数借用了absl

2. tf.app.run默认会读取sys.modules["__main__"].main函数

```
from absl.app import run as _run

@tf_export(v1=['app.run'])
def run(main=None, argv=None):
  """Runs the program with an optional 'main' function and 'argv' list."""

  main = main or _sys.modules['__main__'].main

  _run(main=main, argv=argv, flags_parser=_parse_flags_tolerate_undef)
```

3. absl.app.run启动main，会加上性能分析模块

```
def _run_main(main, argv):
  """Calls main, optionally with pdb or profiler."""
  if FLAGS.run_with_pdb:
    sys.exit(pdb.runcall(main, argv))
  elif FLAGS.run_with_profiling or FLAGS.profile_file:
    # Avoid import overhead since most apps (including performance-sensitive
    # ones) won't be run with profiling.
    import atexit
    if FLAGS.use_cprofile_for_profiling:
      import cProfile as profile
    else:
      import profile
    profiler = profile.Profile()
    if FLAGS.profile_file:
      atexit.register(profiler.dump_stats, FLAGS.profile_file)
    else:
      atexit.register(profiler.print_stats)
    retval = profiler.runcall(main, argv)
    sys.exit(retval)
  else:
    sys.exit(main(argv))
```


### 示例

```
import tensorflow as tf

def main(argv):
    hello = tf.constant('Hello, TensorFlow!')
    sess = tf.Session()
    print(sess.run(hello))

if __name__ == "__main__":
    # tensorflow2版本是tf.compat.v1.app.run
    tf.app.run()

python test.py  --run_with_profiling
```



### 总结

1. 方便的命令行参数
2. 包一层性能分析模块


[此生必看的科学实验-水知道答案](http://v.youku.com/v_show/id_XMjgzMzcwNDk4OA)
[印光大师十念法（胡小林主讲第1集）](http://v.youku.com/v_show/id_XMzUwMzc4NzY4NA)