+++
title = 'cmake引用jansson库'
date = 2024-01-09T14:51:43+08:00
summary = '在ubuntun系统上使用clion开发c程序, cmake如何引入jansson库'
categories = ["c"]
tags = ["c", "json", "jansson", "cmake"]
draft = false
+++

### 描述

在ubuntun系统上使用clion开发c程序, cmake如何引入jansson库.

### 下载jansson

```shell
wget http://digip.org/jansson/releases/jansson-2.13.tar.gz

tar -xvf
```

### 编译jansson
```shell
cd jansson-2.13
mkidr build
cd build
cmake ../
make
```

### 查看编译结果

```shell
├── include
│      ├── jansson_config.h
│      └── jansson.h
├── lib
│     └── libjansson.a

```


### clion创建C程序
File->New->Project->选中"C Executable"

### CMakeLists.txt引用jansson

```make
cmake_minimum_required(VERSION 3.22)
project(demo_task C)

set(CMAKE_C_STANDARD 11)

#自定义库, jansson放在了source目录下
set(LIBHOME "/home/xxx/workspace/source/")

# jansson
include_directories(${LIBHOME}/jansson-2.13/build/include)
link_directories(${LIBHOME}/jansson-2.13/build/lib)
link_libraries(jansson)

add_executable(demo_task main.c)

```

### main.c使用jansson

创建demo.json并放入任意json数据, jansson读取demo.json,并打印结果

```c
#include <stdio.h>
#include <jansson.h>

int main() {
    json_t *json;
    json_error_t error;

    json = json_load_file("../demo.json", 0, &error);
    if(!json) {
        /* the error variable contains error information */
    }
    char *result = json_dumps(json, 0);
    printf("%s\n", result);
    free(result);

    return 0;
}

```


### 运行

clion运行并查看结果

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)

[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)

[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)

[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)