# yolov1的缺点

YOLOv1虽然检测速度很快，但是在检测精度上却不如R-CNN系检测方法，YOLOv1在物体定位方面（localization）不够准确，并且召回率（recall）较低。

# 解决方法

### 1.Batch Normaliza

Batch Normalization可以提升模型收敛速度，而且可以起到一定正则化效果，降低模型的过拟合。在YOLOv2中，每个卷积层后面都添加了Batch Normalization层，并且不再使用droput。使用Batch Normalization后，YOLOv2的mAP提升了2.4%。

### 2.**High Resolution Classifier**

用更高分辨率的输入（448x448）之前是（224x224）。

### 3.**Convolutional With Anchor Boxes**

#### 以前 yolo1存在的问题

在YOLOv1中，输入图片最终被划分为 ![[公式]](https://www.zhihu.com/equation?tex=7%5Ctimes7) 网格，每个单元格预测2个边界框。YOLOv1最后采用的是全连接层直接对边界框进行预测，其中边界框的宽与高是相对整张图片大小的，而由于各个图片中存在不同尺度和长宽比（scales and ratios)的物体，YOLOv1在训练过程中学习适应不同物体的形状是比较困难的，这也导致YOLOv1在精确定位方面表现较差。

#### 改进思路

用anchor boxes（先验框）

采用先验框使得模型更容易学习。所以YOLOv2移除了YOLOv1中的全连接层而采用了卷积和anchor boxes来预测边界框。

#### 效果

使用anchor boxes之后，YOLOv2的mAP有稍微下降（这里下降的原因，我猜想是YOLOv2虽然使用了anchor boxes，但是依然采用YOLOv1的训练方法）。YOLOv1只能预测98个边界框（ ![[公式]](https://www.zhihu.com/equation?tex=7%5Ctimes7%5Ctimes2) ），而YOLOv2使用anchor boxes之后可以预测上千个边界框（ ![[公式]](https://www.zhihu.com/equation?tex=13%5Ctimes13%5Ctimes%5Ctext%7Bnum_anchors%7D) )。所以使用anchor boxes之后，YOLOv2的召回率大大提升，由原来的81%升至88%。

### 4.**Dimension Clusters**

先验框的维度（长和宽）都是手动设定的，带有一定的主观性。如果选取的先验框维度比较合适，那么模型更容易学习，从而做出更好的预测。因此，YOLOv2采用k-means聚类方法对训练集中的边界框做了聚类分析。

**聚类分析怎么做的我还没有看**

因为设置先验框的主要目的是为了使得预测框与ground truth的IOU更好，所以聚类分析时选用box与聚类中心box之间的IOU值作为距离指标：

![[公式]](https://www.zhihu.com/equation?tex=d%28box%2C+centroid%29+%3D+1+-+IOU%28box%2C+centroid%29)

### 5.**New Network: Darknet-19**

YOLOv2采用了一个新的基础模型（特征提取器），称为Darknet-19，包括19个卷积层和5个maxpooling层，如图4所示。Darknet-19与VGG16模型设计原则是一致的，主要采用 ![[公式]](https://www.zhihu.com/equation?tex=3%5Ctimes3) 卷积，采用 ![[公式]](https://www.zhihu.com/equation?tex=2%5Ctimes2) 的maxpooling层之后，特征图维度降低2倍，而同时将特征图的channles增加两倍。与NIN([Network in Network](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1312.4400))类似，Darknet-19最终采用global avgpooling做预测，并且在 ![[公式]](https://www.zhihu.com/equation?tex=3%5Ctimes3) 卷积之间使用 ![[公式]](https://www.zhihu.com/equation?tex=1%5Ctimes1) 卷积来压缩特征图channles以降低模型计算量和参数。Darknet-19每个卷积层后面同样使用了batch norm层以加快收敛速度，降低模型过拟合。

#### 性能提升

在ImageNet分类数据集上，Darknet-19的top-1准确度为72.9%，top-5准确度为91.2%，但是模型参数相对小一些。使用Darknet-19之后，YOLOv2的mAP值没有显著提升，但是计算量却可以减少约33%。

### 6.**Direct location prediction**

前面讲到，YOLOv2借鉴RPN网络使用anchor boxes来预测边界框相对先验框的offsets。边界框的实际中心位置 ![[公式]](https://www.zhihu.com/equation?tex=%28x%2Cy%29) ，需要根据预测的坐标偏移值 ![[公式]](https://www.zhihu.com/equation?tex=%28t_x%2C+t_y%29) ，先验框的尺度 ![[公式]](https://www.zhihu.com/equation?tex=%28w_a%2C+h_a%29) 以及中心坐标 ![[公式]](https://www.zhihu.com/equation?tex=%28x_a%2C+y_a%29) （特征图每个位置的中心点）来计算：

![[公式]](https://www.zhihu.com/equation?tex=%5C%5Cx+%3D+%28t_x%5Ctimes+w_a%29-x_a)

![[公式]](https://www.zhihu.com/equation?tex=%5C%5Cy%3D%28t_y%5Ctimes+h_a%29+-+y_a)

但是上面的公式是无约束的，预测的边界框很容易向任何方向偏移，如当 ![[公式]](https://www.zhihu.com/equation?tex=t_x%3D1) 时边界框将向右偏移先验框的一个宽度大小，而当 ![[公式]](https://www.zhihu.com/equation?tex=t_x%3D-1) 时边界框将向左偏移先验框的一个宽度大小，因此每个位置预测的边界框可以落在图片任何位置，这导致模型的不稳定性，在训练时需要很长时间来预测出正确的offsets。

所以，YOLOv2弃用了这种预测方式，而是沿用YOLOv1的方法，就是**预测边界框中心点相对于对应cell左上角位置的相对偏移值**，为了**将边界框中心点约束在当前cell中，使用sigmoid函数处理偏移值**，这样预测的偏移值在(0,1)范围内（每个cell的尺度看做1）。总结来看，根据边界框预测的4个offsets ![[公式]](https://www.zhihu.com/equation?tex=t_x%2C+t_y%2C+t_w%2C+t_h) ，可以按如下公式计算出边界框实际位置和大小：

![[公式]](https://www.zhihu.com/equation?tex=%5C%5Cb_x+%3D+%5Csigma+%28t_x%29%2Bc_x)

![[公式]](https://www.zhihu.com/equation?tex=%5C%5Cb_y+%3D+%5Csigma+%28t_y%29+%2B+c_y)

![[公式]](https://www.zhihu.com/equation?tex=%5C%5Cb_w+%3D+p_we%5E%7Bt_w%7D)

![[公式]](https://www.zhihu.com/equation?tex=%5C%5Cb_h+%3D+p_he%5E%7Bt_h%7D)

其中 ![[公式]](https://www.zhihu.com/equation?tex=%28c_x%2C+x_y%29) 为cell的左上角坐标，如图5所示，在计算时每个cell的尺度为1，所以当前cell的左上角坐标为 ![[公式]](https://www.zhihu.com/equation?tex=%281%2C1%29) 。由于sigmoid函数的处理，边界框的中心位置会约束在当前cell内部，防止偏移过多。而 ![[公式]](https://www.zhihu.com/equation?tex=p_w) 和 ![[公式]](https://www.zhihu.com/equation?tex=p_h) 是先验框的宽度与长度，前面说过它们的值也是相对于特征图大小的，在特征图中每个cell的长和宽均为1。这里记特征图的大小为 ![[公式]](https://www.zhihu.com/equation?tex=%28W%2C+H%29) （在文中是 ![[公式]](https://www.zhihu.com/equation?tex=%2813%2C+13%29) )，这样我们可以将边界框相对于整张图片的位置和大小计算出来（4个值均在0和1之间）：

![[公式]](https://www.zhihu.com/equation?tex=%5C%5Cb_x+%3D+%28%5Csigma+%28t_x%29%2Bc_x%29%2FW)

![[公式]](https://www.zhihu.com/equation?tex=%5C%5C+b_y+%3D+%28%5Csigma+%28t_y%29+%2B+c_y%29%2FH)

![[公式]](https://www.zhihu.com/equation?tex=%5C%5Cb_w+%3D+p_we%5E%7Bt_w%7D%2FW)

![[公式]](https://www.zhihu.com/equation?tex=+%5C%5Cb_h+%3D+p_he%5E%7Bt_h%7D%2FH)

如果再将上面的4个值分别乘以图片的宽度和长度（像素点值）就可以得到边界框的最终位置和大小了。这就是YOLOv2边界框的整个解码过程。约束了边界框的位置预测值使得模型更容易稳定训练，结合聚类分析得到先验框与这种预测方法，YOLOv2的mAP值提升了约5%。

![preview](https://pic3.zhimg.com/v2-7fee941c2e347efc2a3b19702a4acd8e_r.jpg)

### 7.**Fine-Grained Features**

YOLOv2的输入图片大小为 ![[公式]](https://www.zhihu.com/equation?tex=416%5Ctimes416) ，经过5次maxpooling之后得到 ![[公式]](https://www.zhihu.com/equation?tex=13%5Ctimes13) 大小的特征图，并以此特征图采用卷积做预测。![[公式]](https://www.zhihu.com/equation?tex=13%5Ctimes13) 大小的特征图对检测大物体是足够了，但是对于小物体还需要更精细的特征图（Fine-Grained Features)。

YOLOv2所利用的Fine-Grained Features是 ![[公式]](https://www.zhihu.com/equation?tex=26%5Ctimes26) 大小的特征图（最后一个maxpooling层的输入），对于Darknet-19模型来说就是大小为 ![[公式]](https://www.zhihu.com/equation?tex=26%5Ctimes26%5Ctimes512) 的特征图。passthrough层与ResNet网络的shortcut类似，以前面更高分辨率的特征图为输入，然后将其连接到后面的低分辨率特征图上。前面的特征图维度是后面的特征图的2倍，passthrough层抽取前面层的每个 ![[公式]](https://www.zhihu.com/equation?tex=2%5Ctimes2) 的局部区域，然后将其转化为channel维度，对于 ![[公式]](https://www.zhihu.com/equation?tex=26%5Ctimes26%5Ctimes512) 的特征图，经passthrough层处理之后就变成了 ![[公式]](https://www.zhihu.com/equation?tex=13%5Ctimes13%5Ctimes2048) 的新特征图（特征图大小降低4倍，而channles增加4倍，图6为一个实例），这样就可以与后面的 ![[公式]](https://www.zhihu.com/equation?tex=13%5Ctimes13%5Ctimes1024) 特征图连接在一起形成 ![[公式]](https://www.zhihu.com/equation?tex=13%5Ctimes13%5Ctimes3072) 大小的特征图，然后在此特征图基础上卷积做预测。

![preview](https://pic3.zhimg.com/v2-c94c787a81c1216d8963f7c173c6f086_r.jpg)

另外，作者在后期的实现中借鉴了ResNet网络，不是直接对高分辨特征图处理，而是增加了一个中间卷积层，先采用64个 ![[公式]](https://www.zhihu.com/equation?tex=1%5Ctimes1) 卷积核进行卷积，然后再进行passthrough处理，这样 ![[公式]](https://www.zhihu.com/equation?tex=26%5Ctimes26%5Ctimes512) 的特征图得到 ![[公式]](https://www.zhihu.com/equation?tex=13%5Ctimes13%5Ctimes256) 的特征图。这算是实现上的一个小细节。使用Fine-Grained Features之后YOLOv2的性能有1%的提升。

### 8.**Multi-Scale Training**

由于YOLOv2模型中只有卷积层和池化层，所以YOLOv2的输入可以不限于 ![[公式]](https://www.zhihu.com/equation?tex=416%5Ctimes416) 大小的图片。为了增强模型的鲁棒性，YOLOv2采用了多尺度输入训练策略，具体来说就是在训练过程中每间隔一定的iterations之后改变模型的输入图片大小。

由于YOLOv2的下采样总步长为32，输入图片大小选择一系列为32倍数的值： ![[公式]](https://www.zhihu.com/equation?tex=%5C%7B320%2C+352%2C...%2C+608%5C%7D) ，输入图片最小为 ![[公式]](https://www.zhihu.com/equation?tex=320%5Ctimes320) ，此时对应的特征图大小为 ![[公式]](https://www.zhihu.com/equation?tex=10%5Ctimes10) （不是奇数了，确实有点尴尬），而输入图片最大为 ![[公式]](https://www.zhihu.com/equation?tex=608%5Ctimes608) ，对应的特征图大小为 ![[公式]](https://www.zhihu.com/equation?tex=19%5Ctimes19) 。在训练过程，每隔10个iterations随机选择一种输入图片大小，然后只需要修改对最后检测层的处理就可以重新训练。