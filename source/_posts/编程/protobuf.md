---
title: Protobuf
toc: true
date: 2022-01-09 08:00:00
---

## 介绍

Google protobuf是非常出色的开源工具，在项目中可以用它来作为服务间数据交互的接口，例如rpc服务、数据文件传输等。protobuf为proto文件中定义的对象提供了标准的序列化和反序列化方法，可以很方便的对pb对象进行各种解析和转换。以下以Protobuf3的语法进行示范.

## 定义

- **syntax="proto3";**

> 文件的第一行指定了你使用的是proto3的语法：如果你不指定，protocol buffer 编译器就会认为你使用的是proto2的语法。这个语句必须出现在.proto文件的非空非注释的第一行。

### 数据类型

- strings 默认值是空字符串
- int32 默认是0（编译之后为go语言中的int类型）
- int64 默认是0 （编译之后为go语言中的int64）
- float 默认为0.0 （编译之后为go语言中的 float32）
- double 默认为0.0 （编译之后为go语言中的 float64）
- uint32 （编译之后为go语言中的 uint32）
- uint64 （编译之后为go语言中的 uint64）
- bytes 默认值是空bytes
- bool 默认值是false
- enum 默认值是第一个枚举值（value必须为0)

### 字段修饰符

- `repeated`：用来定义数组，一个字段可以重复出现一定次数（包括零次）
- `required`：值不可为空 (proto3中已删除)
- `optional`：可选字段 (proto3中已删除)
- `singular`：符合语法规则的消息包含零个或者一个这样的字段 (proto3中已删除)
- 默认值： `string code=2 [default=200];` (proto3中已删除)
- 预留字段：`reserved 6 to 8;`

### 其他类型

1. 枚举定义

```protobuf
//枚举类型，必须从0开始，序号可跨越。同一包下不能重名，所以加前缀来区别
enum Role {
	Role_Admin=0;
	Role_Guest=1;
	Role_User=2;
	Role_Other=9;
}
```

1. Map类型

```protobuf
map<key_type, value_type> map_field = N;
```

> 其中key_type可以是任意Integer或者string类型（所以，除了floating和bytes的任意标量类型都是可以的）value_type可以是任意类型。

例如，如果你希望创建一个project的映射，每个Projecct使用一个string作为key

```protobuf
map<string, Project> projects = 3;
```

### 数据校验

默认不支持数据校验，如果直接添加数据校验位protobuf反序列化出错，所以采用的方法有手动封装在自定义的通讯帧中，或者选择数据校验插件。

## 使用

### 配置环境

```
sudo apt install libboost-all-dev
sudo apt install libprotobuf-dev
sudo apt install libprotobuf-c-dev
# Proto生成
sudo apt install protobuf-c-compiler 
sudo apt install protobuf-compiler 
```



### 生成

```bash
 protoc --cpp_out=<path> <XXX.proto>

 protoc --cpp_out="./" protocol.proto 
```

### 复杂cmakelist生成

详情见参考文件

### 技巧和建议

**技巧1:使用optional而不是required字段。**

protobuf的可选字段optional是一个很巧妙的设计，optional字段是可选的，一个optional字段存在与否都不影响proto对象的序列化和反序列化，利用它可以实现数据协议的向后兼容和向前兼容，即以后增加新的字段，或弃用（注意这里是弃用而不是删除）旧字段都不需要修改代码。

相比optional字段，requried字段要求字段必须存在，否则会导致proto解析失败。一旦某个字段被设计为requried类型，将来随着业务的快速发展可能会成为负担，因此在使用requried类型时一定要慎重。

**技巧2:使用bytes而不是string字段。**

这个技巧要感谢身边好同事的建议和提醒，protobuf的bytes和string都能表示字符串，但是string类型会对字符串做utf8格式校验，而bytes不会，因此使用bytes的编解码效率更高。

**技巧3:尽可能只维护一份proto文件。**

世界上唯一不变的事情就是永远在发生变化，精心设计好的proto文件也会随着业务发展而改变。因此，工程中不提倡拷贝分发proto文件，而应通过提供git地址的方式只维护一份proto文件。

**技巧4:尽可能减少对其他proto文件的依赖。**

proto应尽量独立，在某些编程语言如lua中是不支持proto中嵌套其他proto这种方式的，需要手动修改以剔除外部proto依赖，或采用其他办法解决。另外，减少对其他proto文件的依赖也有利于对proto的查看和理解。

**技巧5:利用开源工具实现proto和json的转换。**

protobuf的二进制可读性不好，远不如json的可读性，工程中有很多地方都会涉及proto和json之间的转换。假如你手头上有一个很好用的json开发接口，现在要实现proto和json之间的相互转换，一定要抵制住造轮子的冲动，而应该去找找json2pb这样的开源工具。开源工具只需要几行代码就能搞定这样的需求，又好又快，既能提高工作效率，又可以保证质量。

## 参考资料

[Google protobuf使用技巧和经验](https://www.cnblogs.com/feicheninfo/p/14220110.html)

[protobuf 生成的C++代码详解](https://blog.csdn.net/zyboy2000/article/details/94960025)

[Protobuf在Cmake中的正确使用 ](https://www.cnblogs.com/bigoldpan/p/14502873.html)