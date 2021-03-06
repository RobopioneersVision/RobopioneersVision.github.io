---
title: Deepsort算法解读
toc: true
date: 2022-01-17 1
---



# Deepsort算法解读

## 一.出发点

sort跟踪方法是把检测框和跟踪框的iou情况输入到匈牙利算法中进行线性分配来关联每帧之间的ID,deepsort想要把目标的外观信息加入到匹配计算中,这样在目标被遮盖后来又出现的情况下,还能正确匹配ID,从而可以有效减少ID switch频繁的情况

## 二.算法思路

### 1.轨迹处理和状态估计

状态估计:

用一个8维空间表示轨迹在某时刻的状态:<img src="https://img-blog.csdnimg.cn/20190805203823843.png" alt="img" style="zoom: 50%;" />

其中(u,v)是bounding box的中心坐标,r是长宽比,h表示高度,其余4个变量表示速度信息,使用一个基于常量速度模型和线性观测模型的卡尔曼滤波器对目标运动状态进行预测,预测结果是(u,v,r,h)

轨迹处理:

针对跟踪器:

设置计数器,在卡尔曼滤波期间递增,一旦跟踪结果和检测结果能够match上,这个track相对应的计数器就重置为0;

如果一个track在一段时间内一直没能match上结果,就把这个track从跟踪器列表tracks中删除

针对新检测结果:

当某帧出现新结果的时候(当前追踪结果无法match检测结果),可能出现了新的目标,会为其创建新的跟踪器track,不过需要观察一段时间,如果连续三帧中潜在的新追踪器对目标位置的预测结果都能match,那么确认出现了新的目标轨迹,否则认为出现了虚警,会删除该track

### 2.分配问题

在分配问题上,sort把检测框和追踪框的iou作为输出,采用匈牙利算法,输出检测框和跟踪框的匹配结果,deepsort同时考虑了运动信息的关联和目标外观信息的关联,使用了融合度量的方式计算检测和跟踪轨迹之间的匹配程度

#### 运动信息的关联:

使用检测框与追踪预测框之间的马氏距离来描述运动关联程度:

<img src="https://img-blog.csdnimg.cn/20190805203823857.png" alt="img" style="zoom: 80%;" />

dj表示第j个检测框的位置,yi表示第i个跟踪器对目标的预测位置,Si表示检测位置与平均跟踪位置之间的协方差矩阵.马氏距离通过计算检测位置和平均跟踪位置之间的标准差将状态测量的不确定性进行了考虑

并且deepsort通过以逆χ2分布计算得来的95%置信区间对马氏距离进行阈值化处理

![img](https://img-blog.csdnimg.cn/20190805203823841.png)

如果某次关联的马氏距离小于指定的阈值t(1),则运动状态关联成功,对于四维测量空间.相应的Mahalanobis阈值为t(1)=9.4877

#### 目标外观的关联

当运动的不确定性很低时,马氏距离匹配是一个合适的关联方法,但是在图像空间中使用卡尔曼滤波进行运动状态估计只是一个比较粗糙的预测,特别是相机存在运动时会使马氏距离的关联方法失效,造成ID switch的现象

因此引入了第二种关联方法,对每一个检测块dj求一个特征向量rj(卷积出来的feature向量rj且||rj||=1).对每一个追踪目标构建一个gallary,储存每一个跟踪目标成功关联的最近100帧的特征向量.

第二种度量方法就是计算第i个追踪器的最近100个成功关联的特征集与当前帧第j个检测结果的特征向量间的最小余弦距离计算公式为:

<img src="https://img-blog.csdnimg.cn/20190805203823854.png" alt="img"  />

当轨迹太长时,导致外观发生变化,发生变化后,再使用最小余弦距离作为度量会出问题,所以在计算距离时,轨迹中的检测数量不能太多

如果上面的距离小于阈值,那么关联是成功的(阈值是从单独的训练集中得到的)

#### 两种关联方式的融合

使用两种度量方式的线性加权作为最终的度量:
![img](https://img-blog.csdnimg.cn/20190805203824144.png)

只有当两个指标都满足各自阈值条件的时候才进行融合

距离度量对短期的匹配效果很好,但对长时间的遮挡,使用外观特征的度量比较有效

### 3.级联匹配

当一个目标长时间被遮挡之后,卡尔曼滤波预测的不可能性就会大大增加,状态空间内的可观察性就会大大降低

如果此时两个追踪器竞争同一个检测结果的匹配权,往往遮挡时间较长的那条轨迹因为长时间未更新位置信息,追踪预测位置的不确定性更大即协方差更大,由于马氏距离计算时采用了协方差的倒数因此马氏距离会更小,因此使得预测结果更可能和遮挡时间较长的那条轨道相关联,这种不理想的效果往往会破坏追踪的持续性(本来协方差矩阵是一个正态分布,那么连续的预测不更新就会导致这个正态分布的方差越来越大,那么离均值欧式距离远的点可能和之前分布中离得较近的点获得同样的马氏距离值)

于是采用级联匹配来对更加频繁出现的目标赋予优先权:

Track是物体跟踪集合

Detection是物体检测集合

1.C矩阵存放所有物体跟踪i与物体检测j之间距离的计算结果

2.B矩阵存放所有物体跟踪i与物体检测j之间是否关联的判断(0或1)

3.关联集合Matched初始化为{}

4.将找不到匹配的物体检测集合初始化为Unmatched

5.从刚刚匹配成功的跟踪器循环遍历到最多已经有Amax(最大未匹配次数) 次没有匹配的跟踪器

6.选择满足条件的跟踪器集合Tn

7.根据最小成本算法计算出Tn与物体检测j关联成功产生集合[xi,j]

8.更新Matched为匹配成功的（物体跟踪i，物体检测j） 集合

9.从Unmatched中去除已经匹配成功的物体检测j

10.循环

11.返回 Matched Unmatched 两个集合

即下图：

![7us1zj.jpg](https://s4.ax1x.com/2022/01/12/7us1zj.jpg)

![7uBNdO.png](https://s4.ax1x.com/2022/01/12/7uBNdO.png)

级联匹配的核心思想是由小到大对消失时间相同的轨迹进行匹配,这样首先保证了对最近出现的目标赋予最大的优先权,也解决了上面所述的问题

在匹配的最后阶段还对unconfirmed和age=1的未匹配轨迹进行基于iou的匹配,可以缓减因为表观突变或者部分遮盖导致的较大变化,但有可能导致一些新出现的轨迹被连接到了一些旧的轨迹上(这种情况比较少)

## 三.总体效果

相对于只用相邻帧进行匹配来说,deepsort允许高达30帧(是可以自定义的)的丢失,而卡尔曼的等速运动模型没有改变,这使FP（false positive）升高很多