### 说明

go的反射有什么不同, 与动态语言python有什么不同

### go反射的不同

1. 首先, go是静态强类型。再怎么反射它也是静态语言，不支持动态获取类型，例如，通过字符串"struct_name"，创建struct_name对象

2. go的反射，在于通过对象获取类型信息。例如，通过object，得到Type，然后获取Type的属性

3. reflect的入口是TypeOf和ValueOf。一切的前提是先有对象


[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://doc.sxjy360.top/book/佛說大乘無量壽莊嚴清淨平等覺經(難字注音).pdf)
[净土大经科注2014-doc](http://doc.sxjy360.top/book/净土大经科注2014-doc.zip)
[此生必看的科学实验-水知道答案](http://v.youku.com/v_show/id_XMjgzMzcwNDk4OA)
[印光大师十念法（胡小林主讲第1集）](http://v.youku.com/v_show/id_XMzUwMzc4NzY4NA)