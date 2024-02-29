+++
title = "cmake引用abseil库"
date = 2024-02-29T15:04:00+08:00
summary = "cmake引用abseil库,遇到undefined reference to absl::str_format_internal"
categories = ["cpp"]
tags = ["cpp", "abseil", "cmake"]
draft = false
+++

## 描述

abseil的官方很清晰, 也支持cmake, 照着文档编写一个例子，很快就遇到问题 undefined reference to absl::str_format_internal

## 环境

* github
[https://github.com/abseil/abseil-cpp](https://github.com/abseil/abseil-cpp)

* 官方文档
[https://abseil.io/docs/cpp/quickstart-cmake.html](https://abseil.io/docs/cpp/quickstart-cmake.html)

* git提交

## 编写helloword

1. 下载

```shell
cd third_party
# 我测试的master分支，最后提交时间是2024-02-28
git clone https://github.com/abseil/abseil-cpp.git
```

2. 修改cmake

```cmake
set(ABSL_PROPAGATE_CXX_STD ON)
add_subdirectory(./third_party/abseil-cpp)
# 注意此处链接了str_format, 不是strings被编译成了单独库
target_link_libraries(untitled absl::str_format)
```

3. 测试代码

```cpp
#include <iostream>
#include "absl/strings/str_format.h"

using namespace std;

int main() {
    string server_address = absl::StrFormat("0.0.0.0:%d", 8000);
    cout <<server_address<<endl;
    return 0;
}
```

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)

[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)

[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)

[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)