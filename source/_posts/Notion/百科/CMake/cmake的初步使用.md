---
title: cmake的初步使用.md
toc: true
date: 2021-12-22 09:25:00
---
# cmake的初步使用

1. 在clion中新建一个project 命名为1，打开此project中cMakeList.txt，会出现以下代码

```cpp
cmake_minimum_required(VERSION 3.17)
project(1)

set(CMAKE_CXX_STANDARD 14)

add_executable(1 main.cpp)
```

2. 代码注释

      `cmake_minimum_required`：指定运行此配置文件所需的 CMake 的最低版本；

      `project`：参数值是 `Demo1`，该命令表示项目的名称是 `Demo1` 。

      `add_executable`： 将名为  1 的源文件编译成一个名称为 Demo 的可执行文件。