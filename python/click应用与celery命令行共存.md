### 描述

通过click自定义命令行加载配置，然后调用脚本启动celery worker。click自定义命令行与celery命令行如何共存?


### 问题: 利用@click.pass_context无法传递参数

celery启动worker时, 会覆盖自定义的click, 导致自定义上下文无法传入应用中。


### 解决方法一: 增加celery子命令

通过继承CeleryCommand，增加celery子命令

```
celery myname xxx
```

### 解决方法二: 利用celery的app的属性中

在程序入口地方加载好配方，然后存入app._local属性中, 自定义配置就被传入了worker中， 可以被使用。

```python
@click.command()
@click.option("-c", "--config",
              default=Path(sys.prefix, "etc/xxx/xxx.conf"),
              type=click.Path(exists=True),
              help="config file: --config=  or env AUTUMN_CONF=")
@click.pass_context
def main(ctx, config):
    config_file = Path(config).absolute()

    # 配置
    from xxxx.config.celery import create_app
    # 传入配置，加载之后，保存在属性中
    app = create_app(config_file)

    # worker命令
    # .....
    app.worker_main(argv=param)


if __name__ == "__main__":
    main()
```

