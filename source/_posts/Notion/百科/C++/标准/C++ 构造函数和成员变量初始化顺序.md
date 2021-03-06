---
title: C++ 构造函数和成员变量初始化顺序.md
toc: true
date: 2021-12-22 09:25:00
---
# C++ 构造函数和成员变量初始化顺序

[http://blog.sina.com.cn/s/blog_80ce3a550102wmlb.html](http://blog.sina.com.cn/s/blog_80ce3a550102wmlb.html)

[http://blog.csdn.net/zhaojinjia/article/details/8785912](http://blog.csdn.net/zhaojinjia/article/details/8785912)

[http://www.cnblogs.com/Aion/p/3429515.html](http://www.cnblogs.com/Aion/p/3429515.html)

[https://msdn.microsoft.com/zh-cn/library/aa985615.aspx](https://msdn.microsoft.com/zh-cn/library/aa985615.aspx)[c++构造函数以及类中变量初始化顺序](http://ilovers.sinaapp.com/article/c%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0%E4%BB%A5%E5%8F%8A%E7%B1%BB%E4%B8%AD%E5%8F%98%E9%87%8F%E5%88%9D%E5%A7%8B%E5%8C%96%E9%A1%BA%E5%BA%8F)[http://gaocegege.com/Blog/cpp/cppclass](http://gaocegege.com/Blog/cpp/cppclass)

1、成员变量在使用初始化列表初始化时，与构造函数中初始化成员列表的顺序无关，只与定义成员变量的顺序有关。因为成员变量的初始化次序是根据变量在内存中次序有关，而内存中的排列顺序早在编译期就根据变量的定义次序决定了。这点在EffectiveC++中有详细介绍。

2、如果不使用初始化列表初始化，在构造函数内初始化时，此时与成员变量在构造函数中的位置有关。

3、注意：类成员在定义时，是不能初始化的

4、注意：类中const成员常量必须在构造函数初始化列表中初始化。

5、注意：类中static成员变量，必须在类外初始化。

6、静态变量进行初始化顺序是基类的静态变量先初始化，然后是它的派生类。直到所有的静态变量都被初始化。这里需要注意全局变量和静态变量的初始化是不分次序的。这也不难理解，其实静态变量和全局变量都被放在公共内存区。可以把静态变量理解为带有“作用域”的全局变量。在一切初始化工作结束后，main函数会被调用，如果某个类的构造函数被执行，那么首先基类的成员变量会被初始化。

变量的初始化顺序就应该是：

- 1 基类的静态变量或全局变量
- 2 派生类的静态变量或全局变量
- 3 基类的成员变量
- 4 派生类的成员变量

我想对面向对象有了解的童鞋应该不会对类和对象感到陌生吧 ！

对象并不是突然建立起来的，创建对象必须时必须同时创建父类以及包含于其中的对象。C++遵循如下的创建顺序：

(1)如果某个类具体基类，执行基类的默认构造函数。

(2)类的非静态数据成员，按照声明的顺序创建。

(3)执行该类的构造函数。

即构造类时，会先构造其父类，然后创建类成员，最后调用本身的构造函数。

## 默认构造函数

默认构造函数是指用户在定义一个类时，但并没有显示地定义构造函数，那么编译器会隐式地合成一个构造函数，该默认构造函数会为你完成类成员的构造。如我定义了一个类，该类有三个数据成员，分别为 int 型、string 型、vector 型：

[https://blog.csdn.net/u014489596/article/details/38488927](https://blog.csdn.net/u014489596/article/details/38488927)

多继承下两个以上基类有相同名称的成员函数时，派生类对象引用该名称函数时，若不做任何处理，将不能正常工作。其原因在于，重载规则不适用于跨越多个类的情况。有两种基本的方法：一是使用时明确指出用到哪个基类的同名函数；二是用using声明把不同基类的同名函数引入到一个公共作用域中。

第一种方法举例。

class A {

public:

int func ( int );

void func ( char );

// ...

};

class B {

public:

double func ( double );

// ...

};

class AB : public A, public B {

public:

// ...

};

因为多重继承的这种歧义问题，因此不推荐使用多重继承机制。