---
title: cmake学习（二） 创建库并使用find_package查找包.md
toc: true
date: 2021-12-22 09:25:00
---
# cmake学习（二） 创建库并使用find_package查找包

[https://blog.csdn.net/u014572215/article/details/106105627](https://blog.csdn.net/u014572215/article/details/106105627)

Mastering_CMake的第五章创建自己的包，并在其他的工程中使用find_package查找自己的创建的包

ide是clion，gcc环境是MinGW

工程目录和文件

![](cmake学习（二） 创建库并使用find_package查找包/1.png)

1、创建一个源文件gromit.cpp和头文件gromit.h作为自己创建的包的对象，gromit.cpp中只有一个函数，打印一条消息

```
gromit.h文件
class gromit {
public:
    void info();

};
```

gromit.cpp文件

```
#include <iostream>
#include "gromit.h"

void gromit::info(){
    std::cout << "Hello, info!" << std::endl;
}
```

2、创建项目的包配置文件，项目必须提供程序包配置文件，以便外部应用程序可以找到它们。考虑一个简单的项目“Gromit”，该项目提供了可执行文件以生成源代码以及一个库，生成的代码必须与该库链接。CMakeLists.txt文件的开头可能是：

```
cmake_minimum_required(VERSION 3.15)
project(Gromit)#工程名称
set(version 1.0)#设置版本
# Create library and executable.
#创建静态库，库名称是gromit
add_library(gromit STATIC gromit.cpp gromit.h)

add_executable(gromit-gen gromit_gen.cpp)
target_link_libraries(gromit-gen gromit)#链接库

```

为了安装Gromit并导出其目标以供外部项目使用

```
set(CMAKE_INSTALL_PREFIX D:/gromit)#设置安装位置，将头文件和库都安装到这个文章
# Install and export the targets.
#安装头文件
install(FILES gromit.h DESTINATION include/gromit-${version})
install(TARGETS gromit gromit-gen
        DESTINATION lib/gromit-${version}
        EXPORT gromit-targets)
install(EXPORT gromit-targets
        DESTINATION lib/gromit-${version})
```

最后，Gromit必须在其安装目录中提供一个软件包配置文件，以便外部项目可以使用find_package包来定位它：

```
# Create and install package configuration and version files.
configure_file(
        ${Gromit_SOURCE_DIR}/pkg/gromit-config.cmake.in
        ${Gromit_BINARY_DIR}/pkg/gromit-config.cmake @ONLY)
configure_file(
        ${Gromit_SOURCE_DIR}/gromit-config-version.cmake.in
        ${Gromit_BINARY_DIR}/gromit-config-version.cmake @ONLY)
install(FILES ${Gromit_BINARY_DIR}/pkg/gromit-config.cmake
        ${Gromit_BINARY_DIR}/gromit-config-version.cmake
        DESTINATION lib/gromit-${version})

```

此代码配置并安装程序包配置文件和相应的程序包版本文件。软件包配置输入文件gromit-config.cmake.in中包含代码，

```
# Compute installation prefix relative to this file.
get_filename_component (_dir "${CMAKE_CURRENT_LIST_FILE}" PATH)
get_filename_component (_prefix "${_dir}/../.." ABSOLUTE)
# Import the targets.
include ("${_prefix}/lib/gromit-@version@/gromit-targets.cmake")
# Report other information.
set (gromit_INCLUDE_DIRS "${_prefix}/include/gromit-@version@")
```

相应的软件包版本文件是从其输入文件gromit-config-version.cmake.in配置的，其中包含以下代码

```
set(PACKAGE_VERSION "@version@")
if (NOT "${PACKAGE_FIND_VERSION}" VERSION_GREATER "@version@")
    set(PACKAGE_VERSION_COMPATIBLE 1) # compatible with older
    if ("${PACKAGE_FIND_VERSION}" VERSION_EQUAL "@version@")
        set(PACKAGE_VERSION_EXACT 1) # exact match for this version
    endif ()
endif ()

```

3、安装库，如下图所示，在clion的：Build-Install

![](cmake学习（二） 创建库并使用find_package查找包/2.png)

将在安装路径下生成库和头文件

![](cmake学习（二） 创建库并使用find_package查找包/3.png)

4、完整的CMakeLists.txt文件如下

```
cmake_minimum_required(VERSION 3.15)
project(Gromit)#工程名称
set(version 1.0)#设置版本
# Create library and executable.
#创建静态库，库名称是gromit
add_library(gromit STATIC gromit.cpp gromit.h)

add_executable(gromit-gen gromit_gen.cpp)
target_link_libraries(gromit-gen gromit)#链接库

set(CMAKE_INSTALL_PREFIX D:/gromit)#设置安装位置，将头文件和库都安装到这个文章
# Install and export the targets.
#安装头文件
install(FILES gromit.h DESTINATION include/gromit-${version})
install(TARGETS gromit gromit-gen
        DESTINATION lib/gromit-${version}
        EXPORT gromit-targets)
install(EXPORT gromit-targets
        DESTINATION lib/gromit-${version})

# Create and install package configuration and version files.
configure_file(
        ${Gromit_SOURCE_DIR}/pkg/gromit-config.cmake.in
        ${Gromit_BINARY_DIR}/pkg/gromit-config.cmake @ONLY)
configure_file(
        ${Gromit_SOURCE_DIR}/gromit-config-version.cmake.in
        ${Gromit_BINARY_DIR}/gromit-config-version.cmake @ONLY)
install(FILES ${Gromit_BINARY_DIR}/pkg/gromit-config.cmake
        ${Gromit_BINARY_DIR}/gromit-config-version.cmake
        DESTINATION lib/gromit-${version})

5、创建一个项目调用生成的库gromit
```

![](cmake学习（二） 创建库并使用find_package查找包/4.png)

完整的CMakeLists.txt文件如下

```
cmake_minimum_required(VERSION 3.15)
project(MyProject)

set(CMAKE_CXX_STANDARD 14)
#查找库，查找刚才创建的gromit库
find_package(gromit 1.0 REQUIRED
        PATHS D:/gromit)#设置查找路径，如果没有这个路径，就要设置环境变量
include_directories(${gromit_INCLUDE_DIRS})#添加依赖
message(${gromit_INCLUDE_DIRS})#会输出"D:/gromit/include/gromit-1.0"
# run imported executable
add_custom_command(OUTPUT generated.cpp
        COMMAND gromit-gen generated.cpp)

add_executable(myexe generated.cpp)
target_link_libraries(myexe gromit)
# link to imported library

add_executable(MyProject main.cpp)
target_link_libraries(MyProject gromit)#链接库
```

6、main.cpp主函数如下

```
#include <iostream>
#include "gromit.h"//使用创建的库
int main() {
    std::cout << "Hello, World!" << std::endl;
    gromit g;
    g.info();
    system("pause");
    return 0;
}
```

7、运行结果如下

![](cmake学习（二） 创建库并使用find_package查找包/5.png)

但是无法看到gromit中的info()函数的实现。