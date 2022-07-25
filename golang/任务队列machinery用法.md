### 描述

go实现的基于消息中间件的异步任务队列, 下面是学习笔记

### 使用概述

步骤1: 创建server，配置参数、注册task。(此处server只是个配置作用, 并不是单独的server进程)

步骤2: 启动worker

步骤3: 发送task

与celery的用法是完全一致的


### 创建server

```go

func startServer() (*machinery.Server, error) {
	cnf := &config.Config{
		Broker:          "amqp://guest:guest@localhost:5672/",
		DefaultQueue:    "machinery_tasks",
		ResultBackend:   "amqp://guest:guest@localhost:5672/",
		ResultsExpireIn: 3600,  //任务有效期
		AMQP: &config.AMQPConfig{
			Exchange:      "machinery_exchange",
			ExchangeType:  "direct",
			BindingKey:    "machinery_task",
			PrefetchCount: 3,   //限定消费能力
		},
	}

	// Create server instance
	broker := amqpbroker.New(cnf)
	backend := amqpbackend.New(cnf)
	lock := eagerlock.New()     //任务锁
	server := machinery.NewServer(cnf, broker, backend, lock)

	// Register tasks
	tasks := map[string]interface{}{
		"add":               exampletasks.Add,
		"multiply":          exampletasks.Multiply,
		"sum_ints":          exampletasks.SumInts,
		"sum_floats":        exampletasks.SumFloats,
		"concat":            exampletasks.Concat,
		"split":             exampletasks.Split,
		"panic_task":        exampletasks.PanicTask,
		"long_running_task": exampletasks.LongRunningTask,
	}

	return server, server.RegisterTasks(tasks)
}

```

### 创建worker

创建worker, 之后就可以启动了

```go
func worker() error {
    //消费者的标记
	consumerTag := "machinery_worker"

	server, err := startServer()
	if err != nil {
		return err
	}

    //第二个参数并发数, 0表示不限制
	worker := server.NewWorker(consumerTag, 0)

    //钩子函数
	errorhandler := func(err error) {}
	pretaskhandler := func(signature *tasks.Signature) {}
	posttaskhandler := func(signature *tasks.Signature) {}

	worker.SetPostTaskHandler(posttaskhandler)
	worker.SetErrorHandler(errorhandler)
	worker.SetPreTaskHandler(pretaskhandler)
	return worker.Launch()
}
```

启动结果

```bash
INFO: 2021/05/01 08:28:27 worker.go:58 Launching a worker with the following settings:
INFO: 2021/05/01 08:28:27 worker.go:59 - Broker: amqp://192.168.120.101:5672
INFO: 2021/05/01 08:28:27 worker.go:61 - DefaultQueue: machinery_tasks
INFO: 2021/05/01 08:28:27 worker.go:65 - ResultBackend: amqp://192.168.120.101:5672
INFO: 2021/05/01 08:28:27 worker.go:67 - AMQP: machinery_exchange
INFO: 2021/05/01 08:28:27 worker.go:68   - Exchange: machinery_exchange
INFO: 2021/05/01 08:28:27 worker.go:69   - ExchangeType: direct
INFO: 2021/05/01 08:28:27 worker.go:70   - BindingKey: machinery_task
INFO: 2021/05/01 08:28:27 worker.go:71   - PrefetchCount: 0
INFO: 2021/05/01 08:28:27 amqp.go:96 [*] Waiting for messages. To exit press CTRL+C
```

### 发送任务

```go
server, _ := startServer()

signature := &tasks.Signature{
    Name: "add",
    Args: []tasks.Arg{
        {
            Type:  "int64",
            Value: 1,
        },
        {
            Type:  "int64",
            Value: 1,
        },
    },
}
asyncResult, _ := server.SendTask(signature)
fmt.Println(asyncResult.Get(time.Millisecond * 5))  //等待间隔，理论上是越小越好

//asyncResult.GetWithTimeout(time.Second*120, time.Millisecond * 5)   //第一个参数才是timeout
```

### 总结

以上就是machinery的基本用法,与celery基本一样, 更详细内容参考官方文档


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://doc.sxjy360.top/book/佛說大乘無量壽莊嚴清淨平等覺經(難字注音).pdf)
[净土大经科注2014-doc](http://doc.sxjy360.top/book/净土大经科注2014-doc.zip)