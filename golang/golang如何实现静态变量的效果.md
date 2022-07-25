### 说明

go的结构和方法接收者, 可以模拟类的概念, 然而go不支持静态变量

不能总是每次先创建一个结构体, 然后获取变量吧

该如何办？


### 如何创建"类"

先展示一下，平时我是如何创建"类"的

1.私有的结构体作为真实对象

2. 对外暴露方法，这个方法类似class可以作为参数传递，使用这个class就可以创建对象

3. BaseVar定义对外暴露的接口

4. 定义方法的interface，方便作为参数传递

```go

type BaseVar interface {
    //对外暴露的接口
}

//方便传递
type VarDecriptor func(params ...interface{}) BaseVar

//等同于类
func VarBinary(params ...interface{}) BaseVar {
	s := &varBinary{
		FormatCode: 0o10,
		TextCode: "B",
		PreferredTypes: []reflect.Kind{reflect.Uint8, reflect.Slice},
	}
	return s
}

//实质上的对象
type varBinary struct{
	BaseVar   //外部接口
	FormatCode int
	TextCode	string
	PreferredTypes 	[]reflect.Kind	//bytes bytearray
	value           []byte
}

```

### 静态变量有何意义

关联常量与类

### 解决方法

定义全局变量，以"类"名作为key，存放静态变量。runtime能动态获取"类"名，通过"类"名取值。

```go
var gFormatCode = map[string]int{
	"VarBinary": 1,
}

func FormatCode(f VarDecriptor) int {
    //取方法名
	fname := runtime.FuncForPC(reflect.ValueOf(fc).Pointer()).Name()
	names := strings.Split(fname, ".")
	funcName := names[1]
    //从全局变量中取值
	return gFormatCode[funcName]
}

//使用时通过FormatCode，传入上面的VarBinary，就可以获取常量
```

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://doc.sxjy360.top/book/佛說大乘無量壽莊嚴清淨平等覺經(難字注音).pdf)
[净土大经科注2014-doc](http://doc.sxjy360.top/book/净土大经科注2014-doc.zip)
[此生必看的科学实验-水知道答案](http://v.youku.com/v_show/id_XMjgzMzcwNDk4OA)
[印光大师十念法（胡小林主讲第1集）](http://v.youku.com/v_show/id_XMzUwMzc4NzY4NA)