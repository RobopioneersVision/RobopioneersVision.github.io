---
title: OpenCV的tips.md
toc: true
date: 2021-12-22 09:25:00
---
# OpenCV的tips

## 1.图像的叠加

### 现象

当绘制连续的图像时比如直方图,发现存在之前图像残留现象最终导致之前影像不消失进而最后全画布都是每次显示的叠加.

### 解决

由于每次均是新建的image 推测可能存在一个为新建同大小 Mat的分配的固定数据的地址,导致每次新建指向同一区域进而影像重叠(仅供参考,个人推测),所以每次新建图像除了指定大小格式,再调用scalar进行初始化,即可解决.

```cpp
	
cv::Mat image(800,800,CV_8UC3);//图像叠加
cv::Mat image(800,800,CV_8UC3,cv::Scalar(0,0,0));//successful
```