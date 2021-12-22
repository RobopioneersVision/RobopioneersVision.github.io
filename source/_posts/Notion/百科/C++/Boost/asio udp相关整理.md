---
title: asio udp相关整理.md
toc: true
date: 2021-12-22 09:25:00
---
# asio udp相关整理

@Edwards Kenvay 原创

# 基础

## 同步和异步

通俗来讲，同步操作是指一个进程在执行某个请求之后，若该请求需要执行一段时间才能返回信息，那么在这一段时间内，该线程将会一直等待下去；异步则是在这段时间内，该线程可以继续执行接下来的请求，待信息返回后再进行处理，以提高运行的效率。

## Socket

socket是在应用层和传输层之间的一个抽象层，其目的时将复杂的底层实现抽象为接口，以便于应用层调用来实现网络通信。

## UDP特点

1.占用资源小，传输简单快捷

2.处理速度快

3.实时性较tcp来说强

4.缺少传输保障机制，无法确认数据包是否成功被接收

5.无法保证数据包是否丢失

# ASIO方面

### 同步服务器的简易搭建

1.创建io_context，指定监听端口↓

```cpp
using boost::asio::ip::udp;
boost::asio::io_context io_context;
unsigned short port = 4545;//端口不与其他进程冲突即可
```

2.创建Socket对象，利用asio提供的endpoint封装端口和ip。在这里要说明的是，asio中把通讯双方(client, server)都用endpoint表示，因此在server若要利用endpoint封装ip和端口，则可以使用server本机的ip，调用udp::v4()函数则会直接返回本机的ipv4地址，例子↓

```cpp
udp::socket sock(io_context, udp::endpoint(udp::v4(), port));
```

3.创建接受的client的endpoint封装。上一步我们只是起到了监听的作用，当client向server端发送请求后，server端需要获取client端的ip，进一步进行相关处理，为此我们使用udp::endpoint来封装client的相关信息（有一点值得一提，这里的end_point只是定义，完成封装需监听到client发送数据包，在下一步会有提到）↓

```cpp
udp::endpoint end_point;
```

4.开始监听指定端口。这一步我们通过调用sock.receive_from函数来开始监听，值得注意的是，数据的发送与接收均是以字节流的形式进行的，需要一段连续的内存区域供其读写，boost::asio::buffer便实现了这样的操作，boost::asio::buffer有两个参数，第一个是要写入的地址，第二个则是最大读写大小。sock.receive_from函数的第一个参数即为boost::asio::buffer创建的读写缓冲区，第二个参数则为我们上一步创建的client封装，在这里监听完成后便会完成client相应信息的获取（此外，receive_from还会返回接收到的数据包的大小，在这里我们用length表示）↓

```cpp
size_t length = sock.receive_from(boost::asio::buffer(data, max_length), end_point);
```

5.进行相关操作。第4步完成后，server已经开始4545端口的监听，若接收到请求，则会将发送请求对应的client信息封装到end_point，向data中写入发送内容。我们以回复"Hi didi"为例，简易实现对接受请求后的操作的编写↓

```cpp
sock.send_to(boost::asio::buffer(reply, max_length), end_point);
```

到此，server端的简易同步监听基本上编写完毕，注意这个是一次的，若想一直监听可以放在一个循环里面。

```cpp
#include<boost/asio.hpp>
#include<boost/bind.hpp>
#include <iostream>
using boost::asio::ip::udp;
enum { max_length = 1024 };
void server(boost::asio::io_context& io_context, unsigned short port) {
    udp::socket sock(io_context, udp::endpoint(udp::v4(), port));
    while (true)
    {
        char data[max_length] = "\0";
        udp::endpoint end_point;
        std::cout <<"listening ->"<< "Port "<< port <<"\n";
        size_t length = sock.receive_from(boost::asio::buffer(data, max_length), end_point);
        std::cout << "Receive from ";
        std::cout <<end_point.address()<<" , the message is : "<<data<<"\n"<<"Enter reply:\n";
        char reply[max_length] = "\0";
        std::getchar();
        std::cin.get(reply,max_length);
        sock.send_to(boost::asio::buffer(reply, max_length), end_point);
    }
}
int main()
{
    try
    {
        unsigned short port;
        std::cin >> port;

        boost::asio::io_context context;
        server(context, port);
    }
    catch (const std::exception& e)
    {
        std::cout << e.what();
    }
}
```

### 客户端的简易搭建

1.创建io_context和Socket对象$。$

```cpp
using boost::asio::ip::udp;
boost::asio::io_context context;
udp::socket sock(context, udp::endpoint(udp::v4(), 0));
```

2.使用udp::resolver中的resolve函数将ip和port封装在一个endpoint中。在上面的同步服务器的简易搭建中我们提到过，asio中client与server都用endpoint对象来表示，所以在这里我们将ip和port信息封装为endpoint对象。要注意的是，resolver.resolve函数返回的是一个resolver迭代器，也就是一个endpoint的指针↓

```cpp
udp::resolver resolver(context);
udp::resolver::iterator endpoints = resolver.resolve(udp::v4(), ip, port);
```

3.向client发送请求。在这里我们使用最简单的形式做一个例子↓

```cpp
char request[max_length];
std::cin.get(request, max_length);
size_t request_length = std::strlen(request);
sock.send_to(boost::asio::buffer(request, request_length), *endpoints);
```

4.接收client的处理回复。这里的实现方法与client端的接收大同小异↓

```cpp
sock.send_to(boost::asio::buffer(request, request_length), *endpoints);
char reply[max_length];
udp::endpoint epserver;
size_t reply_length = sock.receive_from(boost::asio::buffer(reply, max_length), epserver);
```

至此，一个简易client端的框架完成了。

完整代码如下↓

```cpp
#include <iostream>
#include<boost/asio.hpp>
#include <boost/asio.hpp>
using boost::asio::ip::udp;
enum { max_length = 1024 };
int main()
{
    try
    {
        boost::asio::io_context context;
        udp::socket sock(context, udp::endpoint(udp::v4(), 0));
        udp::resolver resolver(context);
        std::string ip, port;
        std::cin >> ip;
        std::cin >> port;
        std::getchar();
        udp::resolver::iterator endpoints = resolver.resolve(udp::v4(), ip, port);
        while (true)
        {
            std::cout << "Enter message: \n";
            char request[max_length];
            std::cin.get(request, max_length);
            size_t request_length = std::strlen(request);
            sock.send_to(boost::asio::buffer(request, request_length), *endpoints);
            char reply[max_length];
            udp::endpoint epserver;
            std::cout << "Waiting for reply...\n";
            size_t reply_length = sock.receive_from(boost::asio::buffer(reply, max_length), epserver);
            std::cout << "Reply is :\n";
            std::cout << reply << "\n";
            std::getchar();
        }

    }
    catch (const std::exception& e)
    {
        std::cout << e.what();
    }

}
```