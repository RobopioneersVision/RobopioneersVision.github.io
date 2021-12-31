---
title: YOLOv4 配置使用遇见的问题及解决.md
toc: true
date: 2021-12-22 09:25:00
---
# YOLOv4 配置使用遇见的问题及解决

默认各种要求库已经安装完全更多详细教程参见官方教程
[https://github.com/AlexeyAB/darknet](https://github.com/AlexeyAB/darknet)

## 1. Makefile编译选项

根据自己电脑配置情况，选择对应的编译选项,将默认的0改为1

```makefile
GPU=1
CUDNN=1
CUDNN_HALF=0
OPENCV=1
AVX=1
OPENMP=1
LIBSO=0
ZED_CAMERA=0
ZED_CAMERA_v2_8=0

# set GPU=1 and CUDNN=1 to speedup on GPU
# set CUDNN_HALF=1 to further speedup 3 x times (Mixed-precision on Tensor Cores) GPU: Volta, Xavier, Turing and higher
# set AVX=1 and OPENMP=1 to speedup on CPU (if error occurs then set AVX=0)
# set ZED_CAMERA=1 to enable ZED SDK 3.0 and above
# set ZED_CAMERA_v2_8=1 to enable ZED SDK 2.X
```

## 2.编译失败找不到nvcc

在第75行左右将原来的nvcc更改为本地nvcc文件（不要文件夹是文件！！！）路径区分大小写

```makefile
#NVCC=nvcc

NVCC=/usr/local/cuda/bin/nvcc
```

## 3. 编译时找不到cuda

同上将文件中的指定路径换位本地cuda路径 添加版本号

```makefile
#COMMON+= -DGPU -I/usr/local/cuda/include/

COMMON+= -DGPU -I/usr/local/cuda-10.1/include/
```

## 4. 编译时文件中convolutional_layer等异常

进入clone的darknet 修改src/convolutional_layer.c 中的内容，由于C语言 不支持在for循环内部定义int导致317行及以后存在多处

```c

    for (int i = 0; i < returned_algo_count; i++)
    {
        if (conv_fwd_results[i].status == CUDNN_STATUS_SUCCESS &&
            conv_fwd_results[i].algo != CUDNN_CONVOLUTION_FWD_ALGO_WINOGRAD_NONFUSED &&
            conv_fwd_results[i].memory < free_memory &&
            (conv_fwd_results[i].memory <= workspace_size_specify || cudnn_preference == cudnn_fastest) &&
            conv_fwd_results[i].time < min_time)
        {
            found_conv_algorithm = 1;
            l->fw_algo = conv_fwd_results[i].algo;
            min_time = conv_fwd_results[i].time;
            //printf(" - cuDNN FWD algo: %d, time = %f ms \n", l->fw_algo, min_time);
        }
    }

int i; // 在外部定义，注意不要重复定义
    for (i = 0; i < returned_algo_count; i++)
    {
        if (conv_fwd_results[i].status == CUDNN_STATUS_SUCCESS &&
            conv_fwd_results[i].algo != CUDNN_CONVOLUTION_FWD_ALGO_WINOGRAD_NONFUSED &&
            conv_fwd_results[i].memory < free_memory &&
            (conv_fwd_results[i].memory <= workspace_size_specify || cudnn_preference == cudnn_fastest) &&
            conv_fwd_results[i].time < min_time)
        {
            found_conv_algorithm = 1;
            l->fw_algo = conv_fwd_results[i].algo;
            min_time = conv_fwd_results[i].time;
            //printf(" - cuDNN FWD algo: %d, time = %f ms \n", l->fw_algo, min_time);
        }
    }
```

## 5. 训练是否加载权重

由于我们时全新的自定义训练所以不需要加载权重

## 6. 停止训练后继续训练

启动训练时加载存放于 backup/XXX.weights 

## 7. 可视化展示训练精度

训练时在结尾添加 -map  展示mAP（meaning average precision）在超过1000次后每隔100次根据您data中的vaild指定图片计算一次准确性

## 8. 开始训练时图片不能加载

### Cannot load image XXXXX.jpg

1.可能时图片存放位置train.txt 中添加了不可见的错误字符 在windows系统下和Linux系统下

换行符不一致导致的错误，解决可利用脚本在Linux生成train.txt 或者通过notepad++ 更换为UNIX 

2. 可能时数据指定文件obj.data 问题和解决同上 或者指定位置出现错误

3. 图片本身的问题，图片本身异常的格式转换或者其他问题导致。（有过一次不能加载图片初期以为时8位深灰度图导致，抄网上教程转为24RGB可以运行，但是后来查各种资料发现本身是支持8位深灰度图的并且相应代码有,又尝试了原8位深图又可以了，所以具体原因暂定，可能和运行指令指定网上下载权重的原因（基于RGB））

```csharp
if (im.c == 1) {
                draw_box_width_bw(im, left, top, right, bot, width, 0.8);    // 1 channel Black-White
            }
            else {
                draw_box_width(im, left, top, right, bot, width, red, green, blue); // 3 channels RGB
            }
```