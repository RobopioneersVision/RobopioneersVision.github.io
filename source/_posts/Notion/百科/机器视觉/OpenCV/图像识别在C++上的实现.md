---
title: 图像识别在C++上的实现.md
toc: true
date: 2021-12-22 09:25:00
---
# 图像识别在C++上的实现

# 参考网址

[https://blog.csdn.net/tealex/article/details/51492949](https://blog.csdn.net/tealex/article/details/51492949)

> cv2.findContours()函数 Python
> 

函数的原型为

```python
cv2.findContours(image, mode, method[, contours[, hierarchy[, offset ]]])
```

返回两个值：contours：hierarchy。

## 参数

第一个参数是寻找轮廓的图像；

第二个参数表示轮廓的检索模式，有四种（本文介绍的都是新的cv2接口）：     cv2.RETR_EXTERNAL表示只检测外轮廓     cv2.RETR_LIST检测的轮廓不建立等级关系     cv2.RETR_CCOMP建立两个等级的轮廓，上面的一层为外边界，里面的一层为内孔的边界信息。如果内孔内还有一个连通物体，这个物体的边界也在顶层。     cv2.RETR_TREE建立一个等级树结构的轮廓。

第三个参数method为轮廓的近似办法     cv2.CHAIN_APPROX_NONE存储所有的轮廓点，相邻的两个点的像素位置差不超过1，即max（abs（x1-x2），abs（y2-y1））==1     cv2.CHAIN_APPROX_SIMPLE压缩水平方向，垂直方向，对角线方向的元素，只保留该方向的终点坐标，例如一个矩形轮廓只需4个点来保存轮廓信息     cv2.CHAIN_APPROX_TC89_L1，CV_CHAIN_APPROX_TC89_KCOS使用teh-Chinl chain 近似算法

## 返回值

cv2.findContours()函数返回两个值，一个是轮廓本身，还有一个是每条轮廓对应的属性。

> findContours  函数参数详解 C++
> 

```cpp
findContours( InputOutputArray image, OutputArrayOfArrays contours,
                              OutputArray hierarchy, int mode,
                              int method, Point offset=Point());
```

## 参数

第一个参数：image，单通道图像矩阵，**可以是灰度图**，但更常用的是二值图像，一般是经过Canny、拉普拉斯等边

缘检测算子处理过的二值图像；

第二个参数：contours，定义为**“vector<vector<Point>> contours”，**是一个向量，并且是一个双重向量，**向量**

**内每个元素保存了一组由连续的Point点构成的点的集合的向量**，每一组Point点集就是一个轮廓。

有多少轮廓，向量contours就有多少元素。

第三个参数：hierarchy，定义为**“vector<Vec4i> hierarchy”**，先来看一下Vec4i的定义：

typedef    Vec<int, 4>   Vec4i;

Vec4i是Vec<int,4>的别名，定义了一个“**向量内每一个元素包含了4个int型变量**”的向量。

所以从定义上看，hierarchy也是一个向量，向量内每个元素保存了一个包含4个int整型的数组。

向量hiararchy内的元素和轮廓向量contours内的元素是一一对应的，向量的容量相同。

hierarchy向量内每一个元素的4个int型变量——hierarchy[i][0] ~hierarchy[i][3]，分别表示第

i个轮廓的**后一个轮廓、前一个轮廓、父轮廓、内嵌轮廓的索引编号**。如果当前轮廓没有对应的后一个

轮廓、前一个轮廓、父轮廓或内嵌轮廓的话，则hierarchy[i][0] ~hierarchy[i][3]的相应位被设置为

默认值-1。

第四个参数：int型的mode，定义轮廓的检索模式：

取值一：CV_RETR_EXTERNAL**只检测最外围轮廓**，包含在外围轮廓内的内围轮廓被忽略

取值二：CV_RETR_LIST   检测所有的轮廓，包括内围、外围轮廓，但是检测到的轮廓不建立等级关

系，彼此之间独立，没有等级关系，这就意味着**这个检索模式下不存在父轮廓或内嵌轮廓**，

所以hierarchy向量内所有元素的第3、第4个分量都会被置为-1，具体下文会讲到

取值三：CV_RETR_CCOMP  检测所有的轮廓，但所有轮廓只建立两个等级关系，外围为顶层，若外围

内的内围轮廓还包含了其他的轮廓信息，则内围内的所有轮廓均归属于顶层

取值四：CV_RETR_TREE， 检测所有轮廓，所有轮廓建立一个等级树结构。外层轮廓包含内层轮廓，内

层轮廓还可以继续包含内嵌轮廓。

第五个参数：int型的method，定义轮廓的近似方法：

取值一：CV_CHAIN_APPROX_NONE 保存物体边界上所有连续的轮廓点到contours向量内

取值二：CV_CHAIN_APPROX_SIMPLE **仅保存轮廓的拐点信息**，把所有轮廓拐点处的点保存入contours

向量内，拐点与拐点之间直线段上的信息点不予保留

取值三和四：CV_CHAIN_APPROX_TC89_L1，CV_CHAIN_APPROX_TC89_KCOS使用teh-Chinl chain 近

似**[算法](http://lib.csdn.net/base/datastructure)**

第六个参数：Point偏移量，所有的轮廓信息相对于原始图像对应点的偏移量，**相当于在每一个检测出的轮廓点上加**

**上该偏移量**，**并且Point还可以是负值**！

其他详细相关说明：[https://www.notion.so/rpsvision/C-4e54bafb3b7f48e69d7e0d2269fce8dc#667e478e358041b7a43328e3a7ae310d]()