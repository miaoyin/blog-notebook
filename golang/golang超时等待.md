### 描述

go中如何实现超时等待

### python实现：队列超时等待

```python
queue.get(block=True, timeout=45)
```


### go实现：通道+time计时

select会阻塞两个case，直到其中一个返回。如果是response，则停止计时器; 否则，就返回错误

```go
func _waitResponse(queueChan *chan int, timeout time.Duration) (int, error){
	ticker := time.NewTicker(timeout)
	var response int
	var err error
	select{
	case response = <-*queueChan:
		ticker.Stop()
	case <-ticker.C:
		err = errors.New("wait response timeout")
	}
	return response, err
}
```


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://doc.sxjy360.top/book/佛說大乘無量壽莊嚴清淨平等覺經(難字注音).pdf)
[净土大经科注2014-doc](http://doc.sxjy360.top/book/净土大经科注2014-doc.zip)