---
title: IP直连地址
toc: true
date: 2022-01-14 11:30:00
---



## 运算设备IP名称映射


| 运算终端             		 | IP地址              |
|:-------------------------:|:------------|
| 哨兵下云台Jsonlab     | 192.168.1.112 |
|      | 192.168.1.111 |
| 3号步兵PioneersPascal | 192.168.1.113 |
| 4号步兵PunkShooter    | 192.168.1.110 |
|  备用步兵Gauss		| 192.168.1.114 |
| AGX Odin			| 192.168.1.100 |
|  大载板测试平台 Zeus | 192.168.1.120 |
| 英雄turning        | 192.168.1.115 |
| NX DaVinci |  |





## Nvidia设备设置网线直连

优点

- 固定IP地址,可在局域网下直接搜索并连接
- 可以直接采用一根网线直接连接设备
  - 可以满速转发图像显示实时图像(路由器存在延迟且帧率较低)

### 基本原理

当本地设备想要访问外网时,需要将数据包向网关转发,经过网关再将数据发送到外界.因而可以采用此过程原理,将个人PC作为Nvidia设备的网关,进而可以直接进行数据传输.更加具体的解释可以参考<<计算机网络>>

### Nvidia设备配置

配置本地静态IP+静态网关

```bash
gedit /etc/network/interfaces
vim /etc/network/interfaces
```



```bash
# interfaces(5) file used by ifup(8) and ifdown(8)
# Include files from /etc/network/interfaces.d:
#source-directory /etc/network/interfaces.d

auto eth0
iface eth0 inet static
address 192.168.1.100
netmask 255.255.255.0
gateway 192.168.1.1
```

配置完成后需要重启或者刷新配置

```bash
sudo /etc/init.d/networking restart
```



### 本地网络地址配置

配置网口连接静态IP且为Nvidia设备的网关,设置IPv4地址和网关以及对应的子网掩码即可,其他无需配置.

![image-20220119180511665](https://gitee.com/y_kvm/img/raw/master/picture/20220119180543.png)



