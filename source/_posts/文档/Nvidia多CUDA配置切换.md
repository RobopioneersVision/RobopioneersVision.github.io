---
title: Nvidia多CUDA配置切换
toc: true
date: 2022-01-23 9:00:00
---

> 本文仅针对Ubuntu linux系统，使用前请检查自己的显卡驱动所支持的最大CUDA版本

## 前言

相信做深度学习的小伙伴在复现论文的时候都有遇到过由于cuda版本问题造成的代码不能跑，各种莫名其妙的问题，笔者也是一样。在挣扎过各种方法之后，笔者发现，使用和原始代码中一模一样的环境是最快的解决方案，总之先让代码跑起来看看效果再说，后面如果需要改进，再想办法看是重写还是什么。所以，这里提供一个多版本cuda+python(tensorflow/pytorch环境)的解决方案，方便大家更快的研(fu)究(xian)算(dai)法(ma)。

# nvcc & nvidia-smi

**nvcc**属于CUDA的**编译器**，将程序编译成可执行的二进制文件，**nvidia-smi** 全称是 **NVIDIA**  **System Management Interface** ，是一种**命令行实用工具**，旨在帮助**管理和监控NVIDIA** **GPU设备。**

CUDA有 **runtime API** 和 **Driver API** 两者都i有对应的CUDA版本 nvcc -V显示对应前者的cuda版本，而nvidia-smi对应后者的CUDA版本

用于支持dirver api的必要文件由 **GPU Driver installer**安装，nvidia-smi属于这一类API，而用于支持runtime api的必要文件是由 **CUDA Toolkit installer**安装。 nvcc和cuda Toolkit一起安装的 CUDA compiler-driver tool 它仅知道自身构建的CUDA runtime API的版本，并不知道安装什么版本的GPU dirver，甚至不知道是否安装了 GPU driver

CUDA toolkit installer 通常回集成GPU driver install如果你的CUDA 通过CUDA Toolkit installer 安装那么runtime api 和driver api的版本应该保持一致，也就是说 nvcc -V 和 nvidia-smi现实的版本一致。否则，你可能使用单独的GPU dirver installer 来安装GPU driver，这样就会导致nvidia-smi和nvcc -V 显示的版本不一致。

通常，driver api向下兼容runtime api 即nvidia-smi显示的版本大于nvcc -V 的版本通常不会出现大问题。

## 多版本CUDA安装

**多版本的cuda下载地址在这：https://developer.nvidia.com/cuda-toolkit-archive**

说是多版本，但其实最常用的就是cuda8.0,9.0,9.1,10.0这几个，因此只要有这几个环境，基本上可以应付大多数的源码。

在下载的时候，注意选择.run文件会比较好，如下：

![img](https://gitee.com/y_kvm/img/raw/master/picture/20220123083749.jpeg)根据你自己的平台选择对应设置

比如咱们下载的是cuda9.0，在安装的过程中，前面会有一堆的用户协议需要阅读完毕，然后就是以下几个问题需要注意以下：

Do you accept the previously read EULA?accept/decline/quit: acceptInstall NVIDIA Accelerated Graphics Driver for Linux-x86_64 384.81?(y)es/(n)o/(q)uit: n 

如果在这之前已经安装好更高版本的显卡驱动就不需要再重复安装，如果没有的话，建议通过系统的硬件驱动配置直接安装。

Install the CUDA 9.0 Toolkit?(y)es/(n)o/(q)uit: yEnter Toolkit Location[ default is /usr/local/cuda-9.0 ]: # 一般选择默认即可，也可以选择安装在其他目录，在需要用的时候指向该目录或者使用软连接 link 到 /usr/local/cuda。/usr/local/cuda-9.0 is not writable.Do you wish to run the installation with 'sudo'?(y)es/(n)o: yPlease enter your password: Do you want to install a symbolic link at /usr/local/cuda? #  是否将安装目录通过软连接的方式 link 到 /usr/local/cuda，yes or no  都可以，如果你认为你最常用的版本是cuda9.0，为了方便，可以选择yes，但是这里选择no，后面专门设置也是可以的。(y)es/(n)o/(q)uit: nInstall the CUDA 9.0 Samples? #这里不建议安装，基本上不会看，但是会占一大堆空间(y)es/(n)o/(q)uit: n

经过以上安装完毕之后，/usr/local/会出现一个cuda-9.0的文件夹，用同样的方法去安装cuda8.0，cuda9.1,cuda10.0，总之根据你的需要，比如笔者的文件夹下就是这样的：

![img](https://pics7.baidu.com/feed/024f78f0f736afc3653418cafbbb69c2b6451288.jpeg?token=e5bd7ba2cd0901417fb21a275ae60784)笔者安装了三个版本

## 多版本CUDA切换

首先需要导入实际使用的cuda环境变量，打开.bashrc文件（vim ~/.bashrc），添加下面的语句

```
export CUDA_HOME=/usr/local/cuda 
export PATH=$CUDA_HOME:$PATH
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda/lib64"
```

这就代表，在需要使用cuda的时候，系统会去/usr/local/cuda这个文件夹下面找，但是我们刚刚安装的都是带版本编号的cuda-8.0，怎么办呢？这里就需要创建软连接了，软连接的命令使用如下:

```bash
sudo ln -s /usr/local/cuda-8.0/ /usr/local/cuda
```

这样完成后，/usr/local/下就会出现cuda文件，该文件指向cuda8.0文件夹，即访问该文件就等于访问cuda8.0。如图所示：

![img](https://gitee.com/y_kvm/img/raw/master/picture/20220123083749.png)

此时运行nvcc --version，出现：

nvcc: NVIDIA (R) Cuda compiler driverCopyright (c) 2005-2016 NVIDIA CorporationBuilt on Sun_Sep__4_22:14:01_CDT_2016Cuda compilation tools, release 8.0, V8.0.44

比如说你现在需要安装一个python3.5.2+tensorflow0.2.1+cuda8.0的深度学习环境，进行以下步骤即可。

先使用Anaconda建立一个python3.5.2的python环境；sudo ln -s /usr/local/cuda-8.0/ /usr/local/cudapip install tensorflow-gpu==0.2.1就可以了！因为这个python3.5.2+tensorflow0.2.1+cuda8.0环境一般都是代码里面要求的，肯定是兼容的，我们只要保证cuda版本和作者要求的版本一致就行啦！

比如又需要安装一个python3.6+tensorflow1.7+cuda9.0的环境：

先使用Anaconda建立一个python3.6的python环境；

```bash
sudo ln -s /usr/local/cuda-9.0/ 
/usr/local/cudapip install tensorflow-gpu==1.7.0
```



## 参考资料

[【CUDA】nvcc和nvidia-smi显示的版本不一致？](https://www.jianshu.com/p/eb5335708f2a)

[完美解决由于CUDA版本不匹配造成的各种坑](https://baijiahao.baidu.com/s?id=1663920145053509733&wfr=spider&for=pc&qq-pf-to=pcqq.c2c)