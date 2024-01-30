+++
title = "golang超时等待"
date = 2022-07-25T11:40:00+08:00
categories = ["golang"]
tags = ["golang"]
draft = false
summary = "go中如何实现超时等待"
+++


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


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)