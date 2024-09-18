+++
title = "clion中mingw配置vcpkg"
date = 2024-09-18T17:47:00+08:00
summary = "在windows系统上使用clion+mingw开发cpp,配置vcpkg包管理工具"
categories = ["cpp"]
tags = ["cpp", "vcpkg", "cmake"]
draft = false
+++

### 描述
    习惯了go,python包管理工具的便捷性, 在做c++时, 使用了vcpkg.

### 环境

```shell
* 操作系统: windows
* IDE: CLion 2022.3
* mingw: clion自带C:\Program Files\JetBrains\CLion 2022.3\bin\mingw
```

### 安装vcpkg

```shell
git clone https://github.com/microsoft/vcpkg.git
cd vcpkg && bootstrap-vcpkg.bat

# 配置环境变量, 加入系统windows变量
set "VCPKG_ROOT=C:\path\to\vcpkg"
set PATH=%VCPKG_ROOT%;%PATH%
```

### clion项目

* 创建项目

```shell
File-->New-->C++Executable
```

### vcpkg安装fmt包

```shell
vcpkg install fmt:x64-mingw-static
```

### 添加cmake 参数

![配置参数](../../static/clion-cmake-vcpkg01.jpg)

### cmake配置

```cmake
cmake_minimum_required(VERSION 3.24)
set(VCPKG_TARGET_TRIPLET "x64-mingw-static" CACHE STRING "" FORCE)

project(untitled3)
set(CMAKE_CXX_STANDARD 20)
add_executable(untitled3 main.cpp)

####引用fmt
find_package(fmt CONFIG REQUIRED)
target_link_libraries(untitled3 PRIVATE fmt::fmt)
```

### 检查是否引入成功

![检查引入](../../static/clion-cmake-vcpkg01.jpg)

### main.cpp

```cpp
#include <fmt/core.h>

int main() {
    fmt::print("Hello World!\n");
    return 0;
}
```

### 注意

1. 整个配置过程比较简单. 很快就可以配置好.
2. vcpkg支持不同的编译器, 所以需要安装对应版本的包, mingw对应x64-mingw-static
3. cmake中别少了符号，否则很难找出原因

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)

[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)

[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)

[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)