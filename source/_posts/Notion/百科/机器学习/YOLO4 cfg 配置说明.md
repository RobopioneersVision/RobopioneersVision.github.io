---
title: YOLO4 cfg 配置说明.md
toc: true
date: 2021-12-22 09:25:00
---
# YOLO4 cfg 配置说明

转载自CSDN：[https://blog.csdn.net/phinoo/article/details/83022101](https://blog.csdn.net/phinoo/article/details/83022101)

```python
[net]                        ★ [xxx]开始的行表示网络的一层，其后的内容为该层的参数配置，[net]为特殊的层，配置整个网络

# Testing ★ #号开头的行为注释行，在解析cfg的文件时会忽略该行
# batch=1

# subdivisions=1

# Training

batch=64                     ★ 这儿batch与机器学习中的batch有少许差别，仅表示网络积累多少个样本后进行一次BP
subdivisions=16              ★ 这个参数表示将一个batch的图片分sub次完成网络的前向传播
															★★ 敲黑板：在Darknet中，batch和sub是结合使用的，例如这儿的batch=64，sub=16表示训练的过
															程中将一次性加载64张图片进内存，然后分16次完成前向传播，意思是每次4张，前向传播的循环过程中
															累加loss求平均，待64张图片都完成前向传播后，再一次性后传更新参数
															★★★ 调参经验：sub一般设置16，不能太大或太小，且为8的倍数，其实也没啥硬性规定，看着舒服就好
															batch的值可以根据显存占用情况动态调整，一次性加减sub大小即可，通常情况下batch越大越好，还需
															注意一点，在测试的时候batch和sub都设置为1，避免发生神秘错误！

width=608                    ★ 网络输入的宽width
height=608                   ★ 网络输入的高height
channels=3                   ★ 网络输入的通道数channels
★★★ width和height一定要为32的倍数，否则不能加载网络
★ 提示：width也可以设置为不等于height，通常情况下，width和height的值越大，对于小目标的识别
效果越好，但受到了显存的限制，读者可以自行尝试不同组合

momentum=0.9                 ★ 动量 DeepLearning1中最优化方法中的动量参数，这个值影响着梯度下降到最优值得速度
decay=0.0005                 ★ 权重衰减正则项，防止过拟合

angle=0                      ★ 数据增强参数，通过旋转角度来生成更多训练样本
saturation = 1.5             ★ 数据增强参数，通过调整饱和度来生成更多训练样本
exposure = 1.5               ★ 数据增强参数，通过调整曝光量来生成更多训练样本
hue=.1                       ★ 数据增强参数，通过调整色调来生成更多训练样本

learning_rate=0.001          ★ 学习率决定着权值更新的速度，设置得太大会使结果超过最优值，太小会使下降速度过慢。
															如果仅靠人为干预调整参数，需要不断修改学习率。刚开始训练时可以将学习率设置的高一点，
															而一定轮数之后，将其减小在训练过程中，一般根据训练轮数设置动态变化的学习率。
															刚开始训练时：学习率以 0.01 ~ 0.001 为宜。一定轮数过后：逐渐减缓。
															接近训练结束：学习速率的衰减应该在100倍以上。
															学习率的调整参考https://blog.csdn.net/qq_33485434/article/details/80452941
															★★★ 学习率调整一定不要太死，实际训练过程中根据loss的变化和其他指标动态调整，手动ctrl+c结
															束此次训练后，修改学习率，再加载刚才保存的模型继续训练即可完成手动调参，调整的依据是根据训练
															日志来，如果loss波动太大，说明学习率过大，适当减小，变为1/5，1/10均可，如果loss几乎不变，
															可能网络已经收敛或者陷入了局部极小，此时可以适当增大学习率，注意每次调整学习率后一定要训练久
															一点，充分观察，调参是个细活，慢慢琢磨
															★★ 一点小说明：实际学习率与GPU的个数有关，例如你的学习率设置为0.001，如果你有4块GPU，那
															真实学习率为0.001/4
burn_in=1000                 ★ 在迭代次数小于burn_in时，其学习率的更新有一种方式，大于burn_in时，才采用policy的更新方式
max_batches = 500200         ★ 训练次数达到max_batches后停止学习，一次为跑完一个batch

policy=steps                 ★ 学习率调整的策略：constant, steps, exp, poly, step, sig, RANDOM，constant等方式
															参考https://nanfei.ink/2018/01/23/YOLOv2%E8%B0%83%E5%8F%82%E6%80%BB%E7%BB%93/#more
															steps=400000,450000
															scales=.1,.1                 
														★ steps和scale是设置学习率的变化，比如迭代到400000次时，学习率衰减十倍，45000次迭代时，学
															习率又会在前一个学习率的基础上衰减十倍

[convolutional]              ★ 一层卷积层的配置说明
batch_normalize=1            ★ 是否进行BN处理，什么是BN此处不赘述，1为是，0为不是
filters=32                   ★ 卷积核个数，也是输出通道数
size=3                       ★ 卷积核尺寸
stride=1                     ★ 卷积步长
pad=1                        ★ 卷积时是否进行0 padding,padding的个数与卷积核尺寸有关，为size/2向下取整，如3/2=1
activation=leaky             ★ 网络层激活函数
															★★ 卷积核尺寸3*3配合padding且步长为1时，不改变feature map的大小

# Downsample

[convolutional]              ★ 下采样层的配置说明
batch_normalize=1
filters=64
size=3
stride=2
pad=1
activation=leaky             ★★ 卷积核尺寸为3*3，配合padding且步长为2时，feature map变为原来的一半大小

[shortcut]                   ★ shotcut层配置说明
from=-3                      ★ 与前面的多少次进行融合，-3表示前面第三层
activation=linear            ★ 层次激活函数
......
......
[convolutional]              ★ YOLO层前面一层卷积层配置说明
size=1
stride=1
pad=1
filters=255                  ★ filters=num(预测框个数)*(classes+5)，5的意义是4个坐标加一个置信率，论文中的tx,ty,tw,th,
															c，classes为类别数，COCO为80,num表示YOLO中每个cell预测的框的个数，YOLOV3中为3
															★★★ 自己使用时，此处的值一定要根据自己的数据集进行更改，例如你识别4个类，则：
															filters=3*(4+5)=27,三个fileters都需要修改，切记
activation=linear

[yolo]                       ★ YOLO层配置说明
mask = 0,1,2                 ★  使用anchor的索引，0，1，2表示使用下面定义的anchors中的前三个anchor
anchors = 10,13,  16,30,  33,23,  30,61,  62,45,  59,119,  116,90,  156,198,  373,326
classes=80                   ★ 类别数目
num=9                        ★ 每个grid cell总共预测几个box,和anchors的数量一致。当想要使用更多anchors时需要调大num
jitter=.3                    ★ 数据增强手段，此处jitter为随机调整宽高比的范围，该参数不好理解，在我的源代码注释中有详细说明
ignore_thresh = .7
truth_thresh = 1             ★ 参与计算的IOU阈值大小.当预测的检测框与ground true的IOU大于ignore_thresh的时候，参与
															loss的计算，否则，检测框的不参与损失计算。
															★ 理解：目的是控制参与loss计算的检测框的规模，当ignore_thresh过于大，接近于1的时候，那么参与
															检测框回归loss的个数就会比较少，同时也容易造成过拟合；而如果ignore_thresh设置的过于小，那么
															参与计算的会数量规模就会很大。同时也容易在进行检测框回归的时候造成欠拟合。
															★ 参数设置：一般选取0.5-0.7之间的一个值，之前的计算基础都是小尺度（13*13）用的是0.7，
															（26*26）用的是0.5。这次先将0.5更改为0.7。参考：[https://www.e-learn.cn/content/qita/804953](https://www.e-learn.cn/content/qita/804953)
random=1                     ★ 为1打开随机多尺度训练，为0则关闭
																★★ 提示：当打开随机多尺度训练时，前面设置的网络输入尺寸width和height其实就不起作用了，width
																会在320到608之间随机取值，且width=height，没10轮随机改变一次，一般建议可以根据自己需要修改
																随机尺度训练的范围，这样可以增大batch，望读者自行尝试！
```