---
title: OpenCV源码编译安装
toc: true
date: 2022-01-10 12:00:00
---

在安装前，请确定以下事项已完成

- cuda安装完毕

- cudnn安装完毕

- qt安装完毕

  ```bash
  sudo apt install qt5-default
  ```

  

- tbb安装完毕

  ```bash
  sudo apt install libtbb-dev
  ```


- 视频编码库安装

  ```bash
  sudo apt install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
  ```

- cmake-gui安装完毕

> 正常安装完成Opencv后其imshow窗口支持X11转发,可用ssh异步查看桌面显示的窗口.如果存在异常提示按提示安装对应的库,并重星编译imshow涉及的库函数.常见的有
>
> - gtk2.0+ 参考上述库
> - 音频库不影响使用

1. 下载opencv源代码

   打开终端，并输入以下命令

   #检查并更新相关的下载程序和解压程序以及编译器

   ```
   sudo apt update && sudo apt install -y cmake g++ wget unzip
   ```

   ```bash
   sudo apt install build-essential
   # 图像编码和解码库
   sudo apt install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
   # tbb多线程库和图像格式支持
   sudo apt install libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev
   # 如果需要python3支持的话，安装：
   sudo apt install python3-dev python3-numpy
   # 如果需要ffmpeg支持的话：
   sudo apt install ffmpeg
   ```

   #下载源代码

   ```
   wget -O opencv.zip https://github.com/opencv/opencv/archive/4.x.zip（核心）
   wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/4.x.zip（扩展）
   ```

   

   #解压下载的源代码

   ```bash
   unzip opencv.zip
   unzip opencv_contrib.zip
   ```

   

2. 运行cmake-gui进行make脚本的构建

   ![image-20220121211917398](https://gitee.com/y_kvm/img/raw/master/picture/202201212119534.png)

   选择解压的opencv文件

![image-20220121211932996](https://gitee.com/y_kvm/img/raw/master/picture/202201212119042.png)

​		选择生成位置

![image-20220121211943108](https://gitee.com/y_kvm/img/raw/master/picture/202201212119157.png)

点击Configure，选择Unix Makefiles并运行

![image-20220121211952398](https://gitee.com/y_kvm/img/raw/master/picture/202201212119450.png)

在search中搜索以下选项并设置![image-20220111164204765](OpenCV 源码编译安装/image-20220111164204765.png)

- WITH_QT

![image-20220111164337610](https://gitee.com/y_kvm/img/raw/master/picture/202201212120063.png)

- OPENCV_DNN_CUDA


![image-20220121212012194](https://gitee.com/y_kvm/img/raw/master/picture/202201212120255.png)

- WITH_CUDA


![image-20220121212021564](https://gitee.com/y_kvm/img/raw/master/picture/202201212120632.png)

- WITH_TBB

![image-20220121212035259](https://gitee.com/y_kvm/img/raw/master/picture/202201212120314.png)

- OPENCV_EXTRA_MODULES_PATH


![image-20220121212043860](https://gitee.com/y_kvm/img/raw/master/picture/202201212120908.png)

选择解压出来的opencv_contrib-4.x/modules路径

![image-20220121212049731](https://gitee.com/y_kvm/img/raw/master/picture/202201212120791.png)

点击Configure确定配置并构建

4. 使用make根据cmake给出的脚本进行编译

   使用终端打开构建出来的文件夹，输入make即可进行（速度较慢）

   可以输入make -jx进行多核工作，x为工作的核数

   例：make -j4为使用4核进行工作

   （核数的增多可能会导致链接出现问题，继续输入make -jx可以继续之前的工作）

   （若无论如何都会出现问题建议单核）


​		![image-20220121212105078](https://gitee.com/y_kvm/img/raw/master/picture/202201212121188.png)	

4. 将编译完成的opencv文件安装

   make完成后，在继续在终端输入make install即可