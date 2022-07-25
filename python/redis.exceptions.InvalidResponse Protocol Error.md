### 说明

celery+redis实现异步任务，如果涉及到频繁的io调用，可能会出现错误redis.exceptions.InvalidResponse: Protocol Error

### 错误内容

```
redis.exceptions.InvalidResponse: Protocol Error: {, b'\"status\": \"SUCCESS\", \"result\":xxxx \"traceback\": null, \"children\": [], \"date_done\": \"2020-06-04T07:47:38.317661\", \"task_id\": \"fd4e4fa1-f8cc-4083-a287-ac2b7c7d4472\"}'\r\n"
```

###查看redis源码
                                                   
```
#connection.py
def read_response(self):
    response = self._buffer.readline()
    if not response:
        raise ConnectionError(SERVER_CLOSED_CONNECTION_ERROR)

    byte, response = byte_to_chr(response[0]), response[1:]

    # 从redis源码中可以看出，通信的数据格式不对
    if byte not in ('-', '+', ':', '$', '*'):
        raise InvalidResponse("Protocol Error: %s, %s" %
                              (str(byte), str(response)))
```

### 原因
                                                   
从redis源码中可以看出，通信的数据格式不对.
具体为什么会格式不会, 猜测可能是celery使用了pipeline, socket重连之后，数据格式发生变化


### 解决办法

使用evenetlet进行改造，解决socket问题


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://doc.sxjy360.top/book/佛說大乘無量壽莊嚴清淨平等覺經(難字注音).pdf)
[净土大经科注2014-doc](http://doc.sxjy360.top/book/净土大经科注2014-doc.zip)
[此生必看的科学实验-水知道答案](http://v.youku.com/v_show/id_XMjgzMzcwNDk4OA)
[印光大师十念法（胡小林主讲第1集）](http://v.youku.com/v_show/id_XMzUwMzc4NzY4NA)
                                                   