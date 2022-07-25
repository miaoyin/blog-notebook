### 说明

网络通信需要将go基本数据类型转为字节. go如何做？

### 基本类型

| c类型           | go类型  | 字节长度 |
|--|--|--|
| signed char     | int8    | 1        |
| unsigned char   | uint8   | 1        |
| _Bool           | bool    | 1        |
| short           | int16   | 2        |
| unsigned short  | uint16  | 2        |
| int             | int32   | 4        |
| unsigned int    | uint32  | 4        |
| float           | float32 | 4        |
| long long       | int64   | 8        |
| unsigned l long | uint64  | 8        |
| double          | float64 | 8        |
| char[]          | uint8   | 1        |


### 有符号与无符号转换

* int8/uint->byte或 byte->int8/uint8

1个字节强制类型转换会超范围

```go
// int8 ->byte
var a int8 = -1
byte(a)          // 正常 255

//byte->int8
int8(byte(255))  //异常 constant 255 overflows int8

// byte->int8
var a byte = 255
int8(a)         //正常 -1
```

### 通用方法 Write/Read

* /int8/uint8/int16/uint16/int32/uint32/int64/uint64/float32/float64->[]byte

```go
var a int16 =1
buf := new(bytes.Buffer)
binary.Write(buf, binary.BigEndian, &a)
fmt.Println(buf.Bytes())
```

* []byte ->int8/uint8/int16/uint16/int32/uint32/int64/uint64/float32/float64

```go
b :=[]byte{64, 9, 30, 184, 81, 235, 133, 31}
var a float64
binary.Read(bytes.NewBuffer(b), binary.BigEndian, &a)
fmt.Println(a)
```

### binary.BigEndian方法


```go
import (
	"bytes"
	"encoding/binary"
	"reflect"
)

//=================================
//		数字-->字节
//=================================
func Number2Bytes(value interface{}) []byte{
	result := make([]byte, 0)

	switch v := value.(type){
	case int8:
		return append(result, uint8(v))
	case int16:
		binary.BigEndian.PutUint16(result, uint16(v))
		return result
	case int32:
		binary.BigEndian.PutUint32(result, uint32(v))
		return result
	case int64:
		binary.BigEndian.PutUint64(result, uint64(v))
		return result
	case uint8:
		return append(result, uint8(v))
	case uint16:
		binary.BigEndian.PutUint16(result, v)
		return result
	case uint32:
		binary.BigEndian.PutUint32(result, v)
		return result
	case uint64:
		binary.BigEndian.PutUint64(result, v)
		return result
	case float32:
		buf := new(bytes.Buffer)
		binary.Write(buf, binary.BigEndian, &v)
		return buf.Bytes()
	case float64:
		buf := new(bytes.Buffer)
		binary.Write(buf, binary.BigEndian, &v)
		return buf.Bytes()
	}
	return nil
}

//=================================
//		字节-->数字
//=================================
func Bytes2Number(data []byte, kind reflect.Kind) interface{}{
	switch kind{
	case reflect.Int8:
		return int8(data[0])
	case reflect.Int16:
		return int16(binary.BigEndian.Uint16(data))
	case reflect.Int32:
		return int32(binary.BigEndian.Uint32(data))
	case reflect.Int64:
		return int64(binary.BigEndian.Uint64(data))
	case reflect.Uint8:
		return data[0]
	case reflect.Uint16:
		return binary.BigEndian.Uint16(data)
	case reflect.Uint32:
		return binary.BigEndian.Uint32(data)
	case reflect.Uint64:
		return binary.BigEndian.Uint64(data)
	case reflect.Float32:
		var v float32
		buf := bytes.NewBuffer(data)
		binary.Read(buf, binary.BigEndian, &v)
		return v
	case reflect.Float64:
		var v float64
		buf := bytes.NewBuffer(data)
		binary.Read(buf, binary.BigEndian, &v)
		return v
	}
	return nil
}
```


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://doc.sxjy360.top/book/佛說大乘無量壽莊嚴清淨平等覺經(難字注音).pdf)
[净土大经科注2014-doc](http://doc.sxjy360.top/book/净土大经科注2014-doc.zip)
[此生必看的科学实验-水知道答案](http://v.youku.com/v_show/id_XMjgzMzcwNDk4OA)
[印光大师十念法（胡小林主讲第1集）](http://v.youku.com/v_show/id_XMzUwMzc4NzY4NA)
