
# 刷机
TX2的刷机过程与NX刷机过程无异，使用sdkmanager，在刷机时需要 #注意 
##### 1.不要使用装车时的小载板，要将TX2装载在官方载板上进行刷机。
##### 2.刷机时的主机系统需要为Ubuntu18.04或Ubuntu16.04（可以参照NX刷机文档设置虚拟机刷机）
##### 3.刷机时载板与主机链接要使用官方线
##### 4.如果使用虚拟机要在设置中允许虚拟机访问USB3.0接口
接线演示
[![XQHRSJ.jpg](https://s1.ax1x.com/2022/05/29/XQHRSJ.jpg)

# 设备管理
TX2设备现在分别装在两个箱子中，一个箱子上层为损坏载板（无micro-USB口）下层为4个TX2，一个箱子为完好官方载板，上层装有一个系统完整的TX2，刷机时将其替换下来，刷完后放回。

两个箱子
[![XQH6FU.jpg](https://s1.ax1x.com/2022/05/29/XQH6FU.jpg)

TX2箱
[![XQHrwV.jpg](https://s1.ax1x.com/2022/05/29/XQHrwV.jpg)

官方线（两头有绿色标志）和载板箱
[![XQHgW4.jpg](https://s1.ax1x.com/2022/05/29/XQHgW4.jpg)

TX2硬件信息和刷机过程都可以参照官方文档
```
https://docs.nvidia.com/sdk-manager/system-requirements/index.html
```
在此网址中的“目标设备”中找对应设备的官方文档
具体细节可以参照NX刷机文档

# TX2
TX2官方文档最新一次更新为2020.7，而且由于TX2只能在Ubuntu18.04系统下稳定使用，所以可刷的Jetpack的最高版本为4.6

性能上TX2 < NX < AGX，所以任务中有神经网络参与，且对帧率要求高时，尽量不要使用TX2。在对帧率要求不高的任务中可以使用，如工程自动对位。