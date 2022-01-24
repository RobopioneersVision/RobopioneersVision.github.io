---
title: TX2注意事项
toc: true
date: 2021-10-05 17:16:25
tags: TX2，物资
categories: 设备信息
---





## 硬件

#### USB

RPS战队小型载板在USB3.0两口中，靠近核心的为高速口，远离核心的为半速口。

### 串口



## 软件

### boost编译

**原版cmakelist**

```cmake
find_package(Boost REQUIRED)
target_include_directories(${TARGET} PUBLIC ${Boost_INCLUDE_DIRS})
target_link_libraries(${TARGET} PUBLIC ${Boost_LIBRARIES})
```

**编译发生链接错误**

![image-20220124100327156](https://gitee.com/y_kvm/img/raw/master/picture/20220124100328.png)

**引入所需模块**

```cmake
find_package(Boost REQUIRED COMPONENTS system filesystem)
target_include_directories(${TARGET} PUBLIC ${Boost_INCLUDE_DIRS})
target_link_libraries(${TARGET} PUBLIC ${Boost_LIBRARIES})	
```



## 参考资料
> - []()
> - []()
