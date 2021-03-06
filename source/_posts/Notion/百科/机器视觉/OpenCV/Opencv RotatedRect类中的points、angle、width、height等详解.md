---
title: Opencv RotatedRect类中的points、angle、width、height等详解.md
toc: true
date: 2021-12-22 09:25:00
---
# Opencv RotatedRect类中的points、angle、width、height等详解

[https://blog.csdn.net/mailzst1/article/details/83141632](https://blog.csdn.net/mailzst1/article/details/83141632)

在OpenCV中，经常要用到minAreaRect()函数求最小外接矩形（旋转矩形）。该函数返回一个RotatedRect类对象。

RotatedRect类定义如下：

类中有中心点center、尺寸size（包括width、height）、旋转角度angle共3个成员变量；

成员函数中，points()函数求矩形的4个顶点；boundingRect()函数求包含最小外接矩形，与坐标轴平行（或垂直）的最小矩形。

理解这些变量在图形中的对应关系，是正确应用该类的基础。先上示意图：

![](Opencv RotatedRect类中的points、angle、width、height等详解/1.png)

根据上图，说明以下几点：

1. Opencv采用通用的图像坐标系，左上角为原点O(0,0)，X轴向右递增，Y轴向下递增，单位为像素。

2. 矩形4个顶点位置的确定，是理解其它各变量的基础，其中p[0]点是关键。

顶点p[0]的位置可以这样理解：

ⓐ 如果没有边与坐标轴平行，则Y坐标最大的点为p[0]点，如矩形(2)(3)(4)；

ⓑ 如果有边与坐标轴平行，则有两个Y坐标最大的点。此时，左侧的点为p[0]点。如矩形(1)。

即：Y坐标最大的点为p[0]。如果有两个最大的Y坐标，则左侧点（X坐标较小）为p[0]。

3. p[0]~p[3]按顺时针方向依次排列。

4. p[0]到p[3]之间的距离为宽width，其邻边为高height。

5. 角度angle，是以p[0]顶点，平行于X轴，且与X轴方向相同的射线为始边，按逆时针方向旋转到宽边p[0]p[3]所经过的角度，

取负值，取值范围为(-90, 0]。

6. 中心点center为矩形对角线的交点。