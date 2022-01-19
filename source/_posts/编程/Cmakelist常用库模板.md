---
title: Cmakelist常用库模板
toc: true
date: 2021-09-17 22:26:32
tags: 编程
categories: 编程
---

>| 本章内容基于ubuntu20.04 CMAKE 3.16 构建C++项目


## cmake 学习资料

[cmake 最佳实践](https://github.com/ttroy50/cmake-examples)

## 1 项目基本内容
cmake最低版本需求
```cmake
cmake_minimum_required(VERSION 3.10)
project(Project_Name)
```

设置C++语言版本
```cmake
set(CMAKE_CXX_STANDARD 17 )
```

设置项目名称
```cmake
project(test)
```

常见cmake第三方库链接

```makefile
# 构建项目名称
set(TARGET_NAME test)
```

## CUDA

```makefile
find_package(CUDA REQUIRED)
target_include_directories(${TARGET_NAME} PUBLIC ${CUDA_INCLUDE_DIRS})
```

## ZED2

```makefile
find_package(ZED 3 REQUIRED)
target_include_directories(${TARGET_NAME} PUBLIC ${ZED_INCLUDE_DIRS})
target_link_directories(${TARGET_NAME} PUBLIC ${ZED_LIBRARY_DIR})
```

## Boost

```makefile
find_package(Boost  REQUIRED)
target_include_directories(${TARGET_NAME} PUBLIC ${Boost_INCLUDE_DIRS})
target_link_libraries(${TARGET_NAME} PUBLIC ${Boost_LIBRARIES})
```

## OpenCV4.X

```makefile
find_package(OpenCV REQUIRED)
target_include_directories(${TARGET_NAME} PUBLIC ${OpenCV_INCLUDE_DIRS})
target_link_libraries(${TARGET_NAME} PUBLIC ${OpenCV_LIBRARIES})
```

## Redis++

```makefile
find_path(REDIS_INCLUDE_DIRS "sw")
find_library(REDIS_LIBRARIES "redis++")
target_include_directories(${TARGET_NAME} PUBLIC ${REDIS_INCLUDE_DIRS})
target_link_libraries(${TARGET_NAME} PUBLIC ${REDIS_LIBRARIES})
```

## Protobuf3

```cmake
##protobuf
find_package(Protobuf REQUIRED)
target_include_directories(${TARGET_NAME} PUBLIC ${PROTOBUF_INCLUDE_DIRS})
target_link_libraries(${TARGET_NAME} PUBLIC ${PROTOBUF_LIBRARIES})
```



## 参考资料
> - [cmake如何入门](https://www.zhihu.com/question/58949190/answer/999701073)
> - []()
