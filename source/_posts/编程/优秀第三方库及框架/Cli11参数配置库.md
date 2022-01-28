---
title: Cli11参数配置库
toc: true
date: 2022-01-18 15:00:00
---

以下为Cli11库作者对于一些参数解析库的看法


| Library                                                      | My biased opinion                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Boost Program Options](http://www.boost.org/doc/libs/1_63_0/doc/html/program_options.html) | A great library if you already depend on Boost, but its pre-C++11 syntax is really odd and setting up the correct call in the main function is poorly documented (and is nearly a page of code). A simple wrapper for the Boost library was originally developed, but was discarded as CLI11 became more powerful. The idea of capturing a value and setting it originated with Boost PO. [See this comparison.](https://iscinumpy.gitlab.io/post/comparing-cli11-and-boostpo/) |
| [The Lean Mean C++ Option Parser](http://optionparser.sourceforge.net/) | One header file is great, but the syntax is atrocious, in my opinion. It was quite impractical to wrap the syntax or to use in a complex project. It seems to handle standard parsing quite well. |
| [TCLAP](http://tclap.sourceforge.net/)                       | The not-quite-standard command line parsing causes common shortcuts to fail. It also seems to be poorly supported, with only minimal bugfixes accepted. Header only, but in quite a few files. Has not managed to get enough support to move to GitHub yet. No subcommands. Produces wrapped values. |
| [Cxxopts](https://github.com/jarro2783/cxxopts)              | C++11, single file, and nice CMake support, but requires regex, therefore GCC 4.8 (CentOS 7 default) does not work. Syntax closely based on Boost PO, so not ideal but familiar. |
| [DocOpt](https://github.com/docopt/docopt.cpp)               | Completely different approach to program options in C++11, you write the docs and the interface is generated. Too fragile and specialized. |

After I wrote this, I also found the following libraries:



| Library                                                      | My biased opinion                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [GFlags](https://gflags.github.io/gflags)                    | The Google Commandline Flags library. Uses macros heavily, and is limited in scope, missing things like subcommands. It provides a simple syntax and supports config files/env vars. |
| [GetOpt](https://www.gnu.org/software/libc/manual/html_node/Getopt.html) | Very limited C solution with long, convoluted syntax. Does not support much of anything, like help generation. Always available on UNIX, though (but in different flavors). |
| [ProgramOptions.hxx](https://github.com/Fytch/ProgramOptions.hxx) | Interesting library, less powerful and no subcommands. Nice callback system. |
| [Args](https://github.com/Taywee/args)                       | Also interesting, and supports subcommands. I like the optional-like design, but CLI11 is cleaner and provides direct value access, and is less verbose. |
| [Argument Aggregator](https://github.com/vietjtnguyen/argagg) | I'm a big fan of the [fmt](https://github.com/fmtlib/fmt) library, and the try-catch statement looks familiar. ![+1](https://github.githubassets.com/images/icons/emoji/unicode/1f44d.png) Doesn't seem to support subcommands. |
| [Clara](https://github.com/philsquared/Clara)                | Simple library built for the excellent [Catch](https://github.com/philsquared/Catch) testing framework. Unique syntax, limited scope. |
| [Argh!](https://github.com/adishavit/argh)                   | Very minimalistic C++11 parser, single header. Don't have many features. No help generation?!?! At least it's exception-free. |
| [CLI](https://codesynthesis.com/projects/cli/)               | Custom language and parser. Huge build-system overkill for very little benefit. Last release in 2009, but still occasionally active. |
| [argparse](https://github.com/p-ranav/argparse)              | C++17 single file argument parser. Design seems similar to CLI11 in some ways. The author has several other interesting projects. |

None of these libraries fulfill all the above requirements, or really even come close. As you probably have already guessed, CLI11 does. So, this library was designed to provide a great syntax, good compiler compatibility, and minimal installation fuss.



## 简介

Cli11是一个基于C++11的命令行指令解释器,除此之外提供了丰富的功能和直观的接口配置

## 安装配置

ALL-in-one头文件库,直接拖入项目导入.也可以导入系统路径,在后续cmake中添加路径

### CMAKE

```cmake
if(NOT DEFINED CLI11_DIR)
set (CLI11_DIR "/opt/CLI11" CACHE STRING "CLI11 git repository")
endif()
include_directories(${CLI11_DIR}/include)
```

### 头文件

```c++
#include "CLI/App.hpp"
#include "CLI/Formatter.hpp"
#include "CLI/Config.hpp"
```

## 支持格式

支持的配置文件格式包括 

- TOML ( 支持部分语法)
- INI
- JSON
- 也可以自定义格式

## 用法 

部分API用采用了宏进行简化封装,注意宏的格式

> 当前重点关注配置文件数据的加载和使用,故对于命令行等其他内容不过多展开

### 1. 初始化对象

```c++
CLI::App app("configuration print example");
```

### 2. 设置应用参数选项

设置读取配置文件

```c++
app.set_config(<string:选项名称>,
               [<string:默认配置名称>,
                <string:帮助说明信息>,
                <bool:该参数是否启用,默认false>]);
```

### 3.解析附加指令

读取程序启动时的附加的参数,并进行解析存储到app对象中,如果发生异常抛出的宏

```c++
CLI11_PARSE(app, argc, argv);
```

### 4.获取配置文件名称

参数解析需要config对象所以需要根据文件名称进行读取,下为获取配置文件的文件名称.

```C++
// 方法一
app.get_config_ptr()->as<std::string>()
// 方法二
app["--config"]->as<std::string>()
```

### 5.解析配置文件内容

获取配置文件内容,返回一个vector<ConfigItem> 容器

```c++
config.from_file(<配置文件名称>);
```

获取容器内容,遍历容器即可获得各个键值对的名称和值

#### 键

```c++
string name = items[i].name
string name = items[i].fullname() 	// 用.连接父级名称
```

#### 值

值可能为单个内容也可能是数组,采用空格或者,等字符进行间隔,返回值为vector<string>容器

```
vector<string> content = items[i].input
```

### 6 转化数据格式

 Todo

### 7 存储配置文件

Todo

### 终端调用程序

```bash
./sample_test --help # 打印配置参数信息
```

![image-20220120091258860](https://gitee.com/y_kvm/img/raw/master/picture/20220120091259.png)

### 参考示例

```ini
; Commments are supported, using a ;
; The default section is [default], case insensitive

value = 1
str = "A string"
vector = 1 2 3

; Section map to subcommands
[subcommand]
in_subcommand = Wow
sub.subcommand = true

[subbb]
order = 1 2 3 4
size = 10
```



```c++
#include "CLI11/include/CLI/CLI.hpp"
#include <iostream>
#include <string>

int main(int argc, char **argv) {

    // 实例化对象
    CLI::App app("application discribtion");
    // 配置读取配置文件选项
    app.set_config("--config");
    // 解析参数
    CLI11_PARSE(app, argc, argv);
    CLI::ConfigINI config;
    //获取配置文件名称
    auto items = config.from_file(app["--config"]->as<std::string>());
    for (int i=0;i<items.size();i++)
    {
        // 获取键
        std::cout<<items[i].name<<":";
        // 获取值
        auto content = items[i].inputs;
        // 参数为数组的情况,参数单独的情况下为content[0]
        for(int i=0;i<content.size();i++)
        {
            std::cout<<content[i]<<' ';
        }
    }
}
```



## 其他语法

- 文件校验器,测试文件是否存在

```c++
auto  result = CLI::ExistingFile("a.ini");
std::cout<<result<<std::endl;

File does not exist: a
// 正确返回空串""
```

- `ConfigBase *comment(char cchar)`指定用于启动注释块的字符
- `ConfigBase *arrayBounds(char aStart, char aEnd)`指定数组的开始和结束字符
- `ConfigBase *arrayDelimiter(char aSep)`为数组指定分隔符字符
- `ConfigBase *valueSeparator(char vSep)`指定名称和值之间的分隔符





## 参考文档

[官方github](https://github.com/CLIUtils/CLI11)
