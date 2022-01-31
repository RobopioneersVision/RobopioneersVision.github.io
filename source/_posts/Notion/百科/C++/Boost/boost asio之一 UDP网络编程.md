---
title: Boost::Asio之UDP网络编程.md
toc: true
date: 2021-12-22 09:25:00
---
# boost asio之一 UDP网络编程

[https://blog.csdn.net/weixin_39825378/article/details/83996903](https://blog.csdn.net/weixin_39825378/article/details/83996903)

**目录**

[一、UDP协议简介](https://blog.csdn.net/weixin_39825378/article/details/83996903)

[3.1 io_context](https://blog.csdn.net/weixin_39825378/article/details/83996903)

[3.4 全部代码](https://blog.csdn.net/weixin_39825378/article/details/83996903)

# 一、UDP协议简介

UDP （User Datagram Protocol），用户数据报协议。UDP和TCP协议一起位于TCP/IP协议栈的传输层，在IP协议层之上。它是无连接的，而TCP协议是有连接的。报头结构如下图所示：

# 二、UDP SOCKET编程

## 2.1 UDP通信过程

## 2.2 UDP编程

# 三、asio UDP编程

## 3.1 io_context

io_context提供了I/O核心功能，所有使用asio模块的程序至少有1个io_context对象。

boost::asio::io_context io_context

## 3.2 boost::asio::buffer

在io操作中，对数据的读写大都是在一个缓冲区上进行的，在asio框架中，可以通过asio::buffer函数创建一个缓冲区来提供数据的读写。buffer函数本身并不申请内存，只是提供了一个对现有内存的封装，大小是自动管理的。

## 3.3 asio对IP的封装

boost::asio::udp

boost::asio::upd::socket,用于创建udp协议的套接字

它的常用方法有：

receive_from用于接收udp数据

send_to用于发送udp数据

创建udp套接字

boost::asio::udp::socket udpsock(io_context, endpoint(udp::v4(),port))

udp::v4()指定了sockaddr_in 的family 值为 AF_INET，v6()则指定了AF_INET6

endpoint:是对地址结构的封装。

然后就可以焦勇udpsock.receiver_from(boost::asio::buffer(data, max_length), sender_endpoint),

然后向sender_endpoint发送数据

udpsock.send_to(boost::asio::buffer(data, max_length), sender_endpoint)

## 3.4 全部代码

udpserver.cpp

udpclient.cpp