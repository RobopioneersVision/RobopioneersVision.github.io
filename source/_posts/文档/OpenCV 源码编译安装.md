---
title: OpenCV 源码编译安装
toc: true
date: 2022-01-10 12:00:00
---

在安装前，请确定以下事项已完成

- cuda安装完毕
- cudnn安装完毕
- qt安装完毕
- tbb安装完毕
- cmake-gui安装完毕

1. 下载opencv源代码

   打开终端，并输入以下命令

   #检查并更新相关的下载程序和解压程序以及编译器

   sudo apt update && sudo apt install -y cmake g++ wget unzip

   

   #下载源代码
   wget -O opencv.zip https://github.com/opencv/opencv/archive/4.x.zip（核心）
   wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/4.x.zip（扩展）

   

   #解压下载的源代码
   unzip opencv.zip
   unzip opencv_contrib.zip

   

2. 运行cmake-gui进行make脚本的构建

   ![image-20220111163621476](OpenCV 源码编译安装/image-20220111163621476.png)

   选择解压的opencv文件

![image-20220111163710601](OpenCV 源码编译安装/image-20220111163710601.png)

​		选择生成位置

![image-20220111163816206](OpenCV 源码编译安装/image-20220111163816206.png)

点击Configure，选择Unix Makefiles并运行

![image-20220111163929500](OpenCV 源码编译安装/image-20220111163929500.png)

在search中搜索以下选项并设置![image-20220111164204765](OpenCV 源码编译安装/image-20220111164204765.png)

- WITH_QT![image-20220111164337610](OpenCV 源码编译安装/image-20220111164337610.png)

- OPENCV_DNN_CUDA![image-20220111164435455](OpenCV 源码编译安装/image-20220111164435455.png)

- WITH_CUDA![image-20220111164515370](OpenCV 源码编译安装/image-20220111164515370.png)

- WITH_TBB![image-20220111164631698](OpenCV 源码编译安装/image-20220111164631698.png)

- OPENCV_EXTRA_MODULES_PATH![image-20220111164800672](OpenCV 源码编译安装/image-20220111164800672.png)

  选择解压出来的opencv_contrib-4.x/modules路径![image-20220111164902256](OpenCV 源码编译安装/image-20220111164902256.png)

点击Configure确定配置并构建

4. 使用make根据cmake给出的脚本进行编译

   使用终端打开构建出来的文件夹，输入make即可进行（速度较慢）

   可以输入make -jx进行多核工作，x为工作的核数

   例：make -j4为使用4核进行工作

   （核数的增多可能会导致链接出现问题，继续输入make -jx可以继续之前的工作）

   （若无论如何都会出现问题建议单核）

   ![](OpenCV 源码编译安装/2022-01-11 16-02-08屏幕截图.png)

5. 将编译完成的opencv文件安装

   make完成后，在继续在终端输入make install即可