---
title: OpenCV getStructuringElement函数.md
toc: true
date: 2021-12-22 09:25:00
---
# OpenCV getStructuringElement函数

转自：[https://blog.csdn.net/kksc1099054857/article/details/76569718](https://blog.csdn.net/kksc1099054857/article/details/76569718)

**getStructuringElement函数会返回指定形状和尺寸的结构元素。**

```cpp
Mat getStructuringElement(int shape, Size esize, Point anchor = Point(-1, -1));
```

这个函数的第一个参数表示内核的形状，有三种形状可以选择。

矩形：MORPH_RECT;

交叉形：MORPH_CROSS;

椭圆形：MORPH_ELLIPSE;

第二和第三个参数分别是内核的尺寸以及锚点的位置。一般在调用erode以及dilate函数之前，先定义一个Mat类型的变量来获得getStructuringElement函数的返回值。对于锚点的位置，有默认值Point（-1,-1），表示锚点位于中心点。element形状唯一依赖锚点位置，其他情况下，锚点只是影响了形态学运算结果的偏移。

getStructuringElement函数相关调用如下：

```cpp
int g_nStructElementSize = 3; //结构元素(内核矩阵)的尺寸
	Mat element = getStructuringElement(MORPH_RECT,
		Size(g_nStructElementSize,g_nStructElementSize));

```

**调用之后，调用膨胀与腐蚀函数的时候，第三个参数值保存了getStructuringElement返回值的Mat类型变量。也就是element变量。**

```
 erode( src, dst, element );
```

通常核是一个二位数组，特征是奇数行、奇数列，中心位置对应着感兴趣的像素，数组每一个元素为整数或者浮点数，相对应值的大小对应其权重，比如：

[-1, -1,-1],

[-1, 9, -1],

[-1,-1,-1]

表明感兴趣的像素值权重为9，相邻区域权重为-1，而计算则为将中心像素值乘以9，减去周围所有的像素，如果感兴趣的像素与周围像素存在差异，则该差异会被放大，达到了锐化的目的。【注意】权重加起来和为1，这样做的结果是不会改变图像的亮度，如果权重之和为0，则会得到一个边缘检测核，将边缘转为白色，而非边缘区域转为黑色。