---
title: CUDA程序调优指南（二）：性能调优 - 知乎.md
toc: true
date: 2021-12-22 09:25:00
---
# CUDA程序调优指南（二）：性能调优 - 知乎

[https://zhuanlan.zhihu.com/p/84510732?from_voters_page=true](https://zhuanlan.zhihu.com/p/84510732?from_voters_page=true)

本系列文章是我阅读CUDA官方文档以及实践经验所总结而出，如有错误和不足，还请多多指出

目录：

1. CUDA程序调优指南（一）：GPU硬件
2. CUDA程序调优指南（二）：性能调优
3. CUDA程序调优指南（三）：BlockNum和ThreadNumPerBlock

## 3. CUDA程序性能调优

对于一个CUDA kernel function而言，其通常由如下几个部分组成：

- kernel function paras
- local variables
- shared memory with `__syncthreads__` call
- device function call
- loop/if
- `<<<BlocksNum, ThreadsNumPerBlock>>>`

我们分别考虑如何对这些部分进行优化。

### 3.1 kernel function parameters

`__global__` function的参数是存在constant mem里的，并且其大小被限制为4KB。

通常来说，我们传入的参数都比较少，因此这些参数大部分是直接缓存在SM上的register上的，因此读取起来最快。但如果register不够用，那么就会被放到constant mem/cache上，速度就会变慢。

### 3.2 local variables

对于单独的local var，其会被放在register里面，因此读写极快。

对于数组类型的local var，根据访问pattern的不同，速度也不同（见[per-thread array的访问](https://link.zhihu.com/?target=https%3A//devblogs.nvidia.com/fast-dynamic-indexing-private-arrays-cuda/)）

总结而言是这样的：

- 【Static Indexing】：会被放到register里，读写极快
- 【Dynamic indexing with Uniform Access】：会被放到Local Mem里，略慢。但 **更高的math/load比** 配合 **缓存到L1/L2cache**里，也可以很快
- 【Dynamic indexing with Non-uniform Access】：由于会让SM产生多个访存指令，所以最慢。 但可以通过把其放到shared mem里加速

### 3.3 shared memory with `__syncthreads__` call

shared memory就如同2.3节所述，我们需要尽量避免bank confilicts，这样读取最快，一个cycle clock就可以读取128byte。

除此之外，因为shared memory由同一block内的thread共享，所以在初始化shared mem之后，需要调用`__syncthreads__`来对同一block内的所有threads进行同步。

### 3.4 device function calls

编译器会自行决定device function是否会被inlined（多数情况下会inline）。通常来说，inline会更好，因为少了函数调用的开销。

我们可以用**[forceinline](https://link.zhihu.com/?target=https%3A//docs.nvidia.com/cuda/cuda-c-programming-guide/index.html%23noinline-and-forceinline)**来给编译器提示

### 3.5 loop/if

在书写kernel function时，应尽量避免loop/if，因为这两个在代码里引入了分支结构。

如果代码有分支，那么

1. SM首先执行那些进入first branch的threads，此时其余threads在等待
2. 然后执行进入second branch的threads，前面的线程等待。

因此在可能的情况下，我们应该尽量**使用模板参数来替换掉loop/if**：

- 如果`if`所判断的条件在kernel launch时就能确定（即作为kernel的一个parameter传入的），那就可以用模板参数来代替该parameter，这样编译器会自动优化掉另一分支。
- 同理，如果for-loop的边界也在kernel launch时就能确定，那也可用模板参数代替。

针对一些边界是常量（如0->5）的循环，在循环体足够简单的情况下，可以使用`#pragma unroll`

来告诉编译器展开该循环。

### 3.6 屠龙计：BlocksNum, ThreadsNumPerBlock

> 朱泙漫学屠龙于支离益，单千金之家。三年技成，而无所用其巧 ---《庄子·列御寇》
> 

`BlocksNum`和`ThreadsNumPerBlock`是执行kernel function时配置的值。这两个值通常都是经验求解，很难找到最优值。

简单来说，

- `ThreadsNumPerBlock`受限于device property的`MaxThreadsPerBlock`，经验取值为512/1024。
- `BlocksNum`最大无限制，常见求解公式为 。

更详细的见《CUDA程序调优指南（三）：BlockNum和ThreadNumPerBlock》