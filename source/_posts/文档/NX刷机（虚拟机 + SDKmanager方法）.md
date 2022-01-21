---
title: NX刷机
toc: true
date: 2022-01-21 21:00:00
---



#  NX刷机

nx刷机可以通过sd卡烧录（我们的没有sd插口），也可以通过与电脑直接链接通过SDK manager烧录
#
sdk manager刷机需要ubuntu18.04系统（采用虚拟机）
#
两个步骤：1.安装VMware并安装ubuntu18.04    2.安装sdk manager，配置nx
## 安装VMware，安装ubuntu
### 安装vmware
（注意这一步需要注册vmware的账号并登录）
登录[VMware资源库](https://customerconnect.vmware.com/en/downloads/#all_products)
下载对应操作系统的版本 vmware workstation pro
接下来对exe一步步安装即可

### 安装ubuntu18.04
登录[ubuntu官方镜像](http://releases.ubuntu.com/18.04/)
下载对应操作系统的Desktop image
![](https://s3.bmp.ovh/imgs/2022/01/71e85e2c2b43584c.png)
下载下来的ISO文件先放在你指定的路径下

### 配置虚拟机
![](https://s3.bmp.ovh/imgs/2022/01/d7e959c01f31a4fa.png)
磁盘预留75G（实测不报错），在刷机前配置文件需要下载在本地
点击 文件->新建虚拟机->自定义->然后一直默认->稍后安装操作系统->linux，ubuntu64位->虚拟机名称和储存路径->然后一路默认->创建新虚拟磁盘->一路默认到完成
然后使用iso镜像 左上角虚拟机->设置->cd/dvd->使用iso镜像（指定iso的路径）

进入虚拟机，打开终端输入
```
sudo apt-get update
```
```
sudo apt-get check
```
安装ubuntu的文件安装管理包

##### vmtool
以后还想使用虚拟机的话建议配置vmtool（方便与主机文件传输和操作，具体好处可以自行搜索）
点击上侧 虚拟机 -> 安装vmtool
然后按照如下操作[vmtool配置步骤](https://www.cnblogs.com/chjxbt/p/11082845.html)
## 安装sdk manager
如下操作（在ubuntu虚拟机中操作）
[sdk manager的下载网址和下载后的配置步骤](https://blog.csdn.net/caiguanhong/article/details/114550412)
## 配置sdk manager，NX连线（重要）

刷机前接好线
![](https://s3.bmp.ovh/imgs/2022/01/ecb24fd388767262.png)
![](https://s3.bmp.ovh/imgs/2022/01/58cd6145213984fb.jpg)

首先确认nx是否已经刷上了系统（刚购回的nx商家可能已经配过了系统）（可以直接连显示屏看是否有ui界面）
如果nx上没有系统，就连接2，3引脚（GUN和FC_REC）
如果有系统就可以不用连了，但是先进入ubuntu终端输入
```
sudo apt-get update
```
```
sudo apt-get check
```
安装ubuntu的文件安装管理包

前面说的内存问题，如果仍提示内存不足就再扩展磁盘（关闭虚拟机）（设置->磁盘->扩展）

配置记录
![](https://s3.bmp.ovh/imgs/2022/01/f7a6a09d60eb1294.png)
![](https://s3.bmp.ovh/imgs/2022/01/1658e02806083c4a.png)
勾选accept
![](https://s3.bmp.ovh/imgs/2022/01/cca0801da23a2db6.png)

前面检查过一开始是否有系统，如果已经有系统，那么就设置为Automatic Setup
进入nx的ubuntu，打开终端，输入
```
ifconfig -a
```
查看ip地址
将ip地址，用户名，密码填入

如果没有刷过系统就设置为manual setup（手动配置）
Flash Jetson OS完成后连接nx的显示屏上就会出现配置页面，注意别忘了配置apt文件包，下一步配置前如果购买的NX上换了SSD存储，按如下操作，若为tf卡则不需要![[jetson NX SSD设置为第1启动项说明.docx]]
配置完后和上面一样在manager上填入ip地址，用户名，密码
这一步可能会出现nx未进入recovery模式的问题，进入终端输入
```
lsusb
```
![](https://s3.bmp.ovh/imgs/2022/01/338ad73d89d1e35e.png)
ID 0955:7020 NVIDIA Crop代表未进入刷机模式
ID 0955:7e19 NVIDIA Crop代表进入刷机模式（一直不进入就物理重启一次）

STEP3等待时间较长，耐心等待即可


# 问题记录：

### 如果长时间卡在99%，可以尝试换源
[具体配置步骤](https://blog.csdn.net/darnell888/srticle/details/106644437)
出现无法安全用该源更新就是nx没联网，检查网络


### 可能出现的问题：
E: Error, pkgProblemResolver::Resolve generated breaks, this may be caused by held packages
gsettings-desktop-schemas : break :mutter(< 3.31.4) but 3.28.4-0ubuntuu18.04.2 is being setup
解决方案：（虚拟机和nx都配置）
```
sudo apt-get dist-upgrade
```
或者
```
sudo apt install gsettings-desktop-shemas

sudo apt-get install build-essential
```

### 可能出现的问题：
虚拟机上出现E: Sub-proces returned an error code
解决方案：
```
sudo apt-get install remove libqppstream3

sudo apt remove libappstream3

sudo apt-get purge libqppstream4
```

### 可能出现的问题
sdk manager ： Access to APT repository and ability to install Debian packages with it.
解决方案：检查nx联网

### 可能出现的问题
安装 VPI on HOST 模块时 ：host ：APT system is broken and requires manual fix
解决方案：
检查了nx的python-numpy为最新版，但VPI on HOST的依赖版本为python2.7-numpy，先跳过

### 可能出现的问题
安装 SDK component 模块时 可能会有默认ip无效的提示
解决方案：
重启nx

### 资源记录
[官方源](https://mirrors.ustc.edu.cn/repogen/)
[官方deb包](https://launchpad.net/ubuntu/bionic/arm64)
## 注意：
VPI on HOST和Multimedia最后没有成功安装