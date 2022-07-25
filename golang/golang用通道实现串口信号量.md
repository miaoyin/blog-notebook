### 描述

串口通信，使用权该谁？需要采用信号量，进行控制。go如何实现?


### 原理

1. 缓冲通道的缓冲大小等于资源数

2. time计时器控制超时


### 示例

```go

func NewPortAvailableSem()*PortAvailableSem{
	ps := &PortAvailableSem{
		PortChan: make(chan int, 1),
	}
	ps.PortChan <- 1
	return ps
}

type PortAvailableSem struct{
	PortChan chan int
}

func (ps *PortAvailableSem) Acquire(block bool, timeout int) bool{
	if block{
		ticker := time.NewTicker(time.Second*time.Duration(timeout))
		select{
		case  <- ps.PortChan:
			ticker.Stop()
			return true
		case <-ticker.C:
			return false
		}
	}else{
		select{
		case <- ps.PortChan:
			return true
		default:
			return false
		}
	}
	return false
}

func (ps *PortAvailableSem) Release(){
	if ps.Locked(){
		select{
			case ps.PortChan <- 1:
		}
	}
}

func (ps *PortAvailableSem) Locked() bool{
	return len(ps.PortChan) == 0
}

```

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://doc.sxjy360.top/book/佛說大乘無量壽莊嚴清淨平等覺經(難字注音).pdf)
[净土大经科注2014-doc](http://doc.sxjy360.top/book/净土大经科注2014-doc.zip)

