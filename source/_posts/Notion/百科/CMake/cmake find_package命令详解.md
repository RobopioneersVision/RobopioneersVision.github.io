---
title: cmake find_package命令详解.md
toc: true
date: 2021-12-22 09:25:00
---
# cmake find_package命令详解

[https://blog.csdn.net/rangfei/article/details/109177100?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.control&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.control](https://blog.csdn.net/rangfei/article/details/109177100?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.control&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.control)

# 1. 说明

cmake的find_package命令对于构建软件提供了极大的便利，虽然知道怎么使用已经能解决大部分问题，但是cmake支持哪些包？为什么它能找到需要的包？如果是自己编写的库，需要如何做才能使用该命令进行构建等原理性问题却始终困扰着我。
 这篇文章参考cmake官方文档和网上前辈的文章记录自己对find_package命令的学习和分析。

## 1.1 cmake支持哪些包

理论上只要符合find_package命令的查找条件，能让该命令识别的包或者库都会被支持。在实际使用时，我们可以把这些包分成三类：

1. 
   
    官方预定义的包 为了方便我们查找依赖包，cmake为我们包含了许多默认的依赖包，这些包是cmake发行版的一部分，也就是说可以直接在CMakeLists.txt文件中引用，不需要额外安装。它们的位置通常是在cmake安装目录的Module目录下（比如我的系统内是：/usr/share/cmake-3.17/Modules），也可以通过变量CMAKE_MODULE_PATH来指定。 cmake预支持的包可以查看[cmake-modules](https://cmake.org/cmake/help/latest/manual/cmake-modules.7.html)
    
2. 
   
    非官方但支持cmake的包 这一类包虽然不在cmake的预支持列表中，但是自行下载使用cmake编译安装后也能被cmake识别，使用方法与官方预定义包一致。比如glog包。
    
3. 
   
    默认不支持cmake的包 最典型的是我们自己编写的库文件，这类文件既不在cmake的官方列表，安装时默认也不会被cmake搜索到，此时就需要我们自行编写相应的配置文件并放到对应的路径中去。
    

## 1.2 cmake如何找到需要的包

cmake并不是直接去查找包本身，而是通过查找包对应的配置文件来查找包。这个文件说明了包相关的一些信息，比如版本、目录等。在不同模式下，配置文件可能不同，但是只要目标包有对应的.cmake文件且存在于cmake的查找路径中，就可以被find_package直接使用。

# 2. 原理

cmake定义了一系列的路径去查找包的配置文件，同时会定义<PackageName>_FOUND、
 <PackageName>_INCLUDE_DIR、 <PackageName>_INCLUDES _LIBRARY或者<PackageName>_LIBRARIES等变量来保存包的查找状态。

你可以通过<PackageName>_FOUND 来判断模块是否被找到，如果没有找到，按照工程的需要关闭 某些特性、给出提醒或者中止编译，上面的例子就是报出致命错误并终止构建。 如果<PackageName>_FOUND 为真，则将<PackageName>_INCLUDE_DIR 加入 INCLUDE_DIRECTORIES。
 对于cmake的查找路径，不得不提到find_package的两种模式（MODULE模式和CONFIG模式），其中MODULE模式是基本用法，CONFIG模式是高级用法。除非用户显式设置，否则cmake会首先在MODULE模式下查找，在查找失败时会进入CONFIG模式继续查找。

## 2.1 MODULE模式

使用上，MODULE模式比CONFIG模式要简单地多，通常情况下使用这一类命令就足够完成我们的要求，只有在MODULE模式找不到相应包的情况下，我们才可能会需要在CONFIG模式下使用。

### 用法

```
find_package(<PackageName> [version] [EXACT] [QUIET] [MODULE]
             [REQUIRED] [[COMPONENTS] [components...]]
             [OPTIONAL_COMPONENTS components...]
             [NO_POLICY_SCOPE])

```

- PackageName：依赖包名称；
- version：版本号，如果指定，那么find_package一定要检查找到的包是否与与version版本兼容；
- EXACT：如果指定，表示必须完全匹配version指定版本的包而不是仅仅兼容就可以；
- QUIET：如果指定，表示disable信息输出，即命令运行时不输出相关message，即使没有找到该包。但是如果REQUIRED被指定，还是会有信息输出；
- MODULE：表示只用于module模式，即使没有找到也不会进入CONFIG模式；
- REQUIRED：表示此包是必须的，如果没有找到，构建程序报错并终止；
- [COMPONENTS] [components…]：表示查找的包中必须要找到的组件(components），如果有任何一个找不到就算失败，类似于REQUIRED，导致cmake停止执行；
- OPTIONAL_COMPONENTS components…：可选的依赖组件，即使找不到也不会影响cmake继续执行；
- NO_POLICY_SCOPE：cmake policy，参见：[cmake_policy](https://cmake.org/cmake/help/latest/command/cmake_policy.html#command:cmake_policy)

> 注：上述参数中，除了PackageName必需，其他都是可选参数。
> 

### MODULE模式的查找路径

在MODULE模式下，cmake通过查找名为Find<PackageName>.cmake的文件来查找包。

1. 首先在变量CMAKE_MODULE_PATH对应的路径中去查找
2. 如果该变量为空或者在该路径下没有找到，则进入cmake的安装目录下查找（比如我这里是/usr/share/cmake_3.17/Module）。
3. 如果以上两个路径下没有找到指定包，则根据命令的MODULE参数设置与否决定是否进入CONFIG模式下继续查找。

## 2.2 CONFIG模式

用户代码通常使用MODULE模式命令就足够使用

### 用法

```
find_package(<PackageName> [version] [EXACT] [QUIET]
             [REQUIRED] [[COMPONENTS] [components...]]
             [OPTIONAL_COMPONENTS components...]
             [CONFIG|NO_MODULE]
             [NO_POLICY_SCOPE]
             [NAMES name1 [name2 ...]]
             [CONFIGS config1 [config2 ...]]
             [HINTS path1 [path2 ... ]]
             [PATHS path1 [path2 ... ]]
             [PATH_SUFFIXES suffix1 [suffix2 ...]]
             [NO_DEFAULT_PATH]
             [NO_PACKAGE_ROOT_PATH]
             [NO_CMAKE_PATH]
             [NO_CMAKE_ENVIRONMENT_PATH]
             [NO_SYSTEM_ENVIRONMENT_PATH]
             [NO_CMAKE_PACKAGE_REGISTRY]
             [NO_CMAKE_BUILDS_PATH] # Deprecated; does nothing.
             [NO_CMAKE_SYSTEM_PATH]
             [NO_CMAKE_SYSTEM_PACKAGE_REGISTRY]
             [CMAKE_FIND_ROOT_PATH_BOTH |
              ONLY_CMAKE_FIND_ROOT_PATH |
              NO_CMAKE_FIND_ROOT_PATH])

```

CONFIG模式命令几乎是MODULE模式命令的扩展，可以看到前面的参数（除MODULE）外都相同。

- CONFIG|NO_MODULE：这两个参数作用一样，如果指定，则cmake不在MODULE模式的路径下查找包，直接进入CONFIG模式；
- NAMES name1 [name2 …]：如果指定，则Config会根据这些指定的name进行搜索，而不是默认的PackageName；
- CONFIGS config1 [config2 …]：用于替换默认的配置文件的集合；如果找到，这些配置文件会被读取并执行；
- HINTS path1 [path2 … ]：
- PATHS path1 [path2 … ]：
- PATH_SUFFIXES suffix1 [suffix2 …]
- NO_DEFAULT_PATH
- NO_PACKAGE_ROOT_PATH
- NO_CMAKE_PATH
- NO_CMAKE_ENVIRONMENT_PATH
- NO_SYSTEM_ENVIRONMENT_PATH
- NO_CMAKE_PACKAGE_REGISTRY
- NO_CMAKE_BUILDS_PATH
- NO_CMAKE_SYSTEM_PATH
- NO_CMAKE_SYSTEM_PACKAGE_REGISTRY
- CMAKE_FIND_ROOT_PATH_BOTH
- ONLY_CMAKE_FIND_ROOT_PATH：
- NO_CMAKE_FIND_ROOT_PATH： 如果设置，跳过<PackageName>_ROOT查找路径

### CONFIG模式的查找路径

在CONFIG模式下，查找路径比MODULE多得多，而且查找的目标配置文件以<PackageName>Config.cmake 或者<lower-case-package-name>-config.cmake命名
 查找顺序为：

> 在cmake变量或者环境变量_ROOT指定的路径下查找，如果命令中设置了NO_CMAKE_FIND_ROOT_PATH或者CMAKE_FIND_USE_PACKAGE_ROOT_PATH变量设置为false则会跳过此路径；
> 

> 在特定的cmake变量指定的位置查找： CMAKE_PREFIX_PATH CMAKE_FRAMEWORK_PATH CMAKE_APPBUNDLE_PATH （如果设置了NO_CMAKE_PATH参数或者将变量CMAKE_FIND_USE_CMAKE_PATH设置为False，那么会跳过这一步）
> 

> cmake特定的环境变量 <PackageName>_DIR CMAKE_PREFIX_PATH CMAKE_FRAMEWORK_PATH CMAKE_APPBUNDLE_PATH 可以通过NO_CMAKE_ENVIRONMENT_PATH来跳过。
> 

> HINT字段指定的路径
> 

> 搜索标准的系统环境变量PATH。 其中如果是以/bin或者/sbin结尾的，会自动转化为其父目录。 通过指定NO_SYSTEM_ENVIRONMENT_PATH来跳过。
> 

> 存储在cmake的"User Package Registry"(用户包注册表)中的路径。 通过设定NO_CMAKE_PACKAGE_REGISTRY，或者：设定CMAKE_FIND_PACKAGE_NO_PACKAGE_REGISTRY为true， 来避开。
> 

> 设定为当前系统定义的cmake变量： CMAKE_SYSTEM_PREFIX_PATH CMAKE_SYSTEM_FRAMEWORK_PATH CMAKE_SYSTEM_APPBUNDLE_PATH 通过设定NO_CMAKE_SYSTEM_PATH来跳过。
> 

> 在cmake的"System Package Registry"(系统包注册表）中查找。 通过设定NO_CMAKE_SYSTEM_PACKAGE_REGISTRY跳过。 或者通过设定CMAKE_FIND_PACKAGE_NO_SYSTEM_PACKAGE_REGISTRY为true。
> 

> 从PATHS字段指定的路径中查找。
> 

## 2.3 总结

find_package命令的查找流程图为：

![](cmake find_package命令详解/1.png)

# 3 示例

## 3.1 cmake预支持的包

以boost库为例

```
find_package(Boost REQUIRED)
if(Boost_FOUND)
  message("Boost Package found")
endif()

```

## 3.2 非官方但支持cmake的包

glob包不在cmake预支持的包列表中，但是使用cmake编译安装后，也可以直接使用find_package来查找该包，用法与预支持的包没有不同。

**安装glog**

```
# clone该项目
git clone https://github.com/google/glog.git 
# 切换到需要的版本 
cd glog
git checkout v0.40  

# 根据官网的指南进行安装
cmake -H. -Bbuild -G "Unix Makefiles"
cmake --build build
cmake --build build --target install

```

此时就可以和预定义包一样去查找了：

## 3.3 不支持cmake的包

比如最典型的我们自己开发编译的动态库等，这里以之前代码中使用math库为例，说明如何在cmake系统中添加对我们的库的支持。

1. 编译库及安装

```
make
make install

```

我这里会将库.so和对应头文件分别安装到目录/usr/local/lib/math和/usr/local/include下。

1. 编写Findmath.cmake 为了方便，我这里直接将此文件放在了math目录下，然后在的CmakeLists.txt中设置了CMAKE_MODULE_PATH变量为当前目录。如果不这么做，可以将Findmath.cmake按照上述Module模式下查找的路径中去。

```
find_path(MATH_INCLUDE_DIR add.h /usr/include/ /usr/local/include ${CMAKE_SOURCE_DIR}/ModuleMode)
find_library(MATH_LIBRARY NAMES math PATHS /usr/lib/math /usr/local/lib/math ${CMAKE_SOURCE_DIR}/ModuleMode)

if (MATH_INCLUDE_DIR AND MATH_LIBRARY)
    set(MATH_FOUND TRUE)
endif (MATH_INCLUDE_DIR AND MATH_LIBRARY)

```

find_path指定了头文件的路径，find_library指定了查找库文件.so的路径。

1. 修改CmakeLists.txt文件 在应用程序对应的CmakeLists.txt文件中添加如下内容：

```
set(CMAKE_MODULE_PATH "/home/xxx/experiment/cmake/math")
message(${CMAKE_MODULE_PATH})
find_package(math REQUIRED)
if(MATH_FOUND)
    message("libmath found")
    target_include_directories(${MODULE_MAIN} PRIVATE ${MATH_INCLUDE_DIR})
    target_link_libraries(${MODULE_MAIN} ${MATH_LIBRARY})
else()
    message("required libmath not found")
endif()

```

1. 运行结果

```
(base) xxx@xxx-desktop:~/experiment/cmake/build$ cmake ..
/home/xxx/experiment/cmake/math
libmath found
-- Configuring done
-- Generating done
-- Build files have been written to: /home/xxx/experiment/cmake/build
(base) xxx@xxx-desktop:~/experiment/cmake/build$ make
[ 50%] Built target utility
[100%] Built target demo
(base) xxx@xxx-desktop:~/experiment/cmake/build$ ./demo 
Hello world!
1 + 2 is: 3
Hello, this is my first cmake sample 3

```

# 参考链接

[Cmake Document](https://cmake.org/cmake/help/latest/command/find_package.html?highlight=find_package#basic-signature)[深入理解CMake(3):find_package()的使用](https://www.jianshu.com/p/39fc5e548310)[Cmake之深入理解find_package()的用法](https://zhuanlan.zhihu.com/p/97369704?utm_source=wechat_session)