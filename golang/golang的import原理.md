### 描述

import是个很重要的东西，golang是怎么import的

1. import查找包的顺序？

2. import的是什么，包还是路径？

3. 自己项目中怎么import

### golang的包管理历史

第一阶段 早期golang，import是直接查找$GOPATH/scr,$GOROOT/scr目录

第二阶段 v1.5开始采用vendor模式，每个项目有一个vendor目录，存放依赖包

第三阶段 v1.12开始增加了go mod用于管理依赖包

部分学习资料比较早，介绍的还是第一、二阶段，从我们直接学习go mod就可以

### go mod到import顺序

1. 在当前项目目录查找module。找到module后，用module_name加相对路径导入。import "module_name/路径xxx..."

2. 未找到，则逐级查找上级目录的go.mod

3. 未找到，则再找$GOPATH,$GOROOT

### module和package是怎么回事？

go只有package，并没有module的概念, go mod才引入，

module可以理解为python的包或者java的jar包。package的集合.

### import的是什么？

import的是目录, 由于一个目录只能有一个package, 实际也是import包

例如 import "gin-template/db/api"

1. 如果目录名与package名一致。 api.xxx方式使用包

2.如果目录名与pakcage名不一致(目录名api,包名abc)。 abc.xxx方式使用

3.总结：import的是目录下的package。引用方式是, 包名.xxx


### 自己项目中怎么引用

import module_name/包的目录路径


### 与其它语言比较

python中import的是py文件/py文件中的属性

java中import的是java文件中的class

golang中import的是目录下的package

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://doc.sxjy360.top/book/佛說大乘無量壽莊嚴清淨平等覺經(難字注音).pdf)
[净土大经科注2014-doc](http://doc.sxjy360.top/book/净土大经科注2014-doc.zip)
[此生必看的科学实验-水知道答案](http://v.youku.com/v_show/id_XMjgzMzcwNDk4OA)
[印光大师十念法（胡小林主讲第1集）](http://v.youku.com/v_show/id_XMzUwMzc4NzY4NA)