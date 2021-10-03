---
title: wiki使用指南
toc: true
date: 2021-10-03 08:31:20
tags:
categories:
---

前置依赖项
> 　- shell 命令行
> 　- 基本Linux操作指令
> 　- git 基本语法
> 　- 能够登录github的网络



## 下载

### 方法一

git 克隆远程仓库到本地

```shell
git clone https://github.com/KVM-Explorer/KVM-Explorer.github.io
```

如果需要更改为自定义的文件夹名,支持嵌套命名如Blog/wiki/XXX

```shell
git clone https://github.com/KVM-Explorer/KVM-Explorer.github.io　<your directory name>
```



### 方法二

登录https://github.com/KVM-Explorer/KVM-Explorer.github.io手动下载仓库，Code -> Download ZIP,记得打星＾_＾

![image-20211003084015737](wiki使用指南/image-20211003084015737.png)



### git终端

用命令行终端进入仓库所在文件夹，此时显示当前仓库所在的分支为source 分支

![image-20211003084319364](wiki使用指南/image-20211003084319364.png)

如果当前分支不是source分支，采用git checkout切换至source分支

### Gitkraken

出于可视化和方便的需要我更为推荐采用deb安装的gitkraken进行git仓库的管理，流程如上

![image-20211003084835430](wiki使用指南/image-20211003084835430.png)

## 安装



### 安装所需依赖

**Nodejs14**

```shell
# Using Ubuntu
curl -fsSL https://deb.nodesource.com/setup_14.x | sudo -E bash -
sudo apt-get install -y nodejs
```

**cnpm**

由于Nodejs 自带的软件管理器为npm在国内不稳定推荐下载cnpm淘宝镜像或对npm进行换源

```
npm install cnpm
```

通过命令行进入仓库所在文件夹,安装wiki所需依赖

```shell
cnpm install
```

## 测试

**生成网页版并在本机查看**

```shell
hexo g
hexo s
```

![image-20211003090827265](wiki使用指南/image-20211003090827265.png)

![image-20211003090847529](wiki使用指南/image-20211003090847529.png)

## 编写

**生成新文档**

```shell
hexo new post "文章名称"
```

进入source -> _post 将对应文件夹和文件移至所需分类目录，生成时即可按照文件层次分类

> 推荐采用typora 进行markdown内容编写，调整图片设置自动保存到对应的asset



## 参考资料
> - [Node.js Binary Distributions](https://github.com/nodesource/distributions/blob/master/README.md)
> - []()
