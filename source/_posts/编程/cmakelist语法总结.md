---
title: cmakelist语法总结
toc: true
date: 2022-01-18 17:00:00
---

Cmakelist语法不区分大小写,但是为了方便管理和进行快速区分,推荐cmakelist函数采用小写+下划线,自定义变量和关键字等采用大写+下划线的方式

## add_library

生成库文件,根据关键字STATIC和SHARED分别可生成.静态库（.a、.lib）和动态库（.so、.dll）

### 静态库

- 静态库对函数库的链接是放在编译时期完成的。
- 程序在运行时与函数库再无瓜葛，移植方便。
- 浪费空间和资源，因为所有相关的目标文件与牵涉到的函数库被链接合成一个可执行文件。 

### 动态库

- 动态库把对一些库函数的链接载入推迟到程序运行的时期。　
- 可以实现进程之间的资源共享。（因此动态库也称为共享库）
- 将一些程序升级变得简单。
- 甚至可以真正做到链接载入完全由程序员在程序代码中控制（**显示调用**）

### 语法

```cmake
add_library(<name> [STATIC | SHARED | MODULE]
            [EXCLUDE_FROM_ALL]
            [<source>...])
```

- <name> 即生成的库文件的名称
- <MODULE> 指插件不被链接到任何目标但是可能会在运行时动态加载到程序,实现类似dlopen的功能
- <source> 该库/模块所涉及的所有源文件

## add_subdirectory

### 语法

```cmake
add_subdirectory(source_dir [binary_dir] [EXCLUDE_FROM_ALL])
```

- <binary_dir> 指明输出文件夹,如果未指定将调用source_dir的位置

### 注意

在使用 `add_subdirectory(source_dir [binary_dir] [EXCLUDE_FROM_ALL])`命令时，如果 `source_dir` 不是当前目录（`CMakeLists.txt` 所在目录，例子中的 `test/` 目录）的子目录，那么就需要**显式**指定 `[binary_dir]` 参数，用于存储 `source_dir` 相关文件。

当前文件夹下,包含有subdirectory时可以直接添加

## target_include_directories

### 语法

```cmake
target_include_directories(<target> [SYSTEM] [BEFORE]
  <INTERFACE|PUBLIC|PRIVATE> [items1...]
  [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...])
```

- target 链接目标
- <INTERFACE> 被依赖 libary 的 user requirement 的会变成当前 target 的 user requirement
- <PRIVATE> 被依赖 libary 的 user requirement 的会变成当前 target 的 build requirement
- <PUBLIC> = <INTERFADCE> + <PRIVATE>  被依赖 libary 的 user requirement 的会变成当前 target 的 build requirement 和 user requirement.
- <Target> 必须由add_executable()或add_library()之类的命令创建的
- [items1]  Directory目标在构建文件夹中的文件夹所在，默认是源代码的文件夹，如果有生成代码需要采用特定变量**${CMAKE_CURRENT_BINARY_DIR}**指定构建的二进制文件夹（build、cmake-build-debug）

## target_link_libraries

### 语法

```cmake
target_link_libraries(<target>
                      <PRIVATE|PUBLIC|INTERFACE> <item>...
                     [<PRIVATE|PUBLIC|INTERFACE> <item>...]...)
```

- 同上
- Item libraries目标所定义的名称，即生成库的名称，可见于构建的二进制文件夹，或者生成库的cmakelist

## 参考资料

[[静态库和动态库的区别](https://www.cnblogs.com/codingmengmeng/p/6046481.html)](https://www.cnblogs.com/codingmengmeng/p/6046481.html)