---
title: 目标追踪Deepsort
toc: true 
date: 2022-01-21 21:00:00
---



# 目标追踪deepsort

## 一.主要步骤

1.获取原始视频帧

2.利用目标检测器对视频帧中的目标继续检测

3.提取检测到的目标的框中的特征，特征包括表观特征(进行特征对比)和运动特征(方便卡尔曼滤波)

4.计算前后两帧目标之间的匹配程度(用匈牙利算法和级联匹配)，为每个追踪到的目标分配ID

## 二.sort流程

sort(Simple Online and Realtime Tracking)算法是deepsort的前身,其核心是卡尔曼滤波算法和匈牙利算法

卡尔曼滤波算法:用当前的一系列运动变量去预测下一时刻的运动变量,第一次检测的结果用来初始化运动变量

匈牙利算法:解决分配问题,把检测框和卡尔曼预测的框做分配,让卡尔曼预测的框找到和自己最匹配的检测框,达到追踪的效果

整个算法的工作流程:

1.将第一帧的detection(通过目标检测检测到的框)创建其对应的track(轨迹信息),将卡尔曼滤波的运动变量初始化,通过卡尔曼滤波预测其对应的框

2.将该帧的detection和上一帧通过track预测的框通过iou进行匹配,再通过iou匹配的结果计算其cost matrix(计算方式是1-iou)

3.将所有cost matrix 作为匈牙利算法的输入,得到线性的匹配的结果,结果有三种:unmatched track(track失效,直接删除),unmatched detections(detections失配,将这样的detections初始化为一个new track),matched track(追踪成功,将其对应的detection通过卡尔曼滤波更新其对应的tracks)

4.反复上述过程直至视频帧结束te

![img](https://img-blog.csdnimg.cn/20210913191555921.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA54Ku5ZOl5bim5L2g5a2m,size_20,color_FFFFFF,t_70,g_se,x_16)

## 三.Deepsort算法流程

sort算法在物体发生遮挡的时候容易丢失自己的ID,deepsort算法在sort算法的基础上增加了matching cascade(级联匹配)和confirmed(新轨迹的确认),track分为confirmed(确定态)和unconfirmed(不确定态),新产生的tracks是不确定态,不确定态的tracks必须要和detections连续匹配一定的次数(默认三次)才可以转化为确定态,确定态的tracks必须和detections连续匹配一定的次数(默认30次)才会被删除

算法流程:

1.初始化运动变量并用卡尔曼滤波预测(track一定是unconfirmed)

2.匹配框并计算cost matrix

3.用匈牙利算法进行线性匹配,得到的结果有三种:unmatched tracks(是不确定态,直接删除),unmatched detections(初始化为一个new tracks),matched tracks

4.重复上述步骤,直至出现confirmed tracks或视频帧结束

5.通过卡尔曼滤波预测confirmed tracks 和confirmed tracks对应的框,将confirmed tracks的框和detections进行级联匹配(只要tracks匹配上都会保存detection的外观特征和运动信息,默认保存前100帧,用外观特征和运动信息与detetions进行级联匹配,因为confirmed tracks,detection匹配的可能性更大)

6.级联匹配的结果有三种:matched tracks,unmatched tracks,unmatched detection,将之前unconfirmed tracks,unmatched tracked和unmatched detections进行iou匹配,并计算cost matrix

7.将上一步得到的cost matrix带入匈牙利算法,将得到的结果进行与3相同的处理

8.重复5~7直到视频帧结束

![img](https://img-blog.csdnimg.cn/2021091319520099.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA54Ku5ZOl5bim5L2g5a2m,size_20,color_FFFFFF,t_70,g_se,x_16)