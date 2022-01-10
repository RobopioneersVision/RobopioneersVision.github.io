---
title: ubuntu双系统的安装
toc: true
date: 2022-01-10 15:13:15
tags: 
categories: 
---
# ubuntu双系统的安装

ps:本教程适用于UEFI的电脑

1. 检查BIOS模式
   win+r输入msinfo32确认![](https://img2018.cnblogs.com/blog/1628751/201904/1628751-20190421143622758-126034661.png)

2. 分配硬盘空间

   右键我的电脑图标，选择管理，磁盘管理

   ![](https://i.bmp.ovh/imgs/2022/01/21fd508ec81e0f4e.png)

​		选择想要用来分配空间的磁盘并分配空间

​		选中并右键选择压缩卷

​		![](https://i.bmp.ovh/imgs/2022/01/fbcc2e2fbee00dc9.png)

​		压缩空间量为给ubuntu系统分配的空间，建议分配100g以上

​		压缩完成后会出现未分配的磁盘空间

![](https://i.bmp.ovh/imgs/2022/01/60c9b297d0fd2568.png)

3. 刻录ubuntu系统盘

   进入官网https://ubuntu.com/download下载

   选择desktop（桌面版）![](https://i.bmp.ovh/imgs/2022/01/6ebf15997e68f66a.png)

   下载镜像文件后刻录系统盘，这里使用软碟通（选择试用即可）

   打开ubuntu镜像

   ![](https://s4.ax1x.com/2022/01/10/7VzGef.md.png)

   菜单栏选择启动，选择写入硬盘映像，格式化u盘后写入即可
   
4. 电脑设置u盘启动并安装系统

   跟随引导来，注意不要联网

   到安装类型时注意选择其他选项![](https://img2018.cnblogs.com/blog/1628751/201904/1628751-20190421144948814-1142280253.png)

   然后手动分区

   ![](https://img2018.cnblogs.com/blog/1628751/201904/1628751-20190421145008459-1880468436.png)
   
   - /efi 启动目录200mb
   
     注意还要在启动引导器的选项中设置，选择efi的分区（不要把windows的选上）
   
   - swap 两倍于电脑内存
   
   - /ubuntu 根目录建议50g左右
   
   - /home  
   
   最后时区选择上海，其余跟随系统设置即可
