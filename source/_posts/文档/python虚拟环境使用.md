---
title: python虚拟环境使用
toc： true
date: 2022-01-24 15:00:00
---

## 必要性

Python 应用经常需要使用一些包第三方包或者模块，有时需要依赖特定的包或者库的版本，所以不能有一个能适应所有 Python  应用的软件环境，很多时候不同的 Python 应用所依赖的版本是冲突的，满足了其中一个，另一个则无法运行，解决这一问题的方法是  虚拟环境。虚拟环境是一个包含了特定 Python  解析器以及一些软件包的自包含目录，不同的应用程序可以使用不同的虚拟环境，从而解决了依赖冲突问题，而且虚拟环境中只需要安装应用相关的包或者模块，可以给部署提供便利。

## 多项目不同依赖的构建

优先建议采用项目下venv虚拟环境构建，不建议使用conda。如果需要特定的python版本，可以选择conda环境，具体内容请在后续i补充。

## 示例

采用Pycahrm对于一个已有项目构建一个新的虚拟环境，利用python自带的venv模块进行构建

### Pycharm

1. 在设置中添加新的Python解释器

![image-20220124151555767](https://gitee.com/y_kvm/img/raw/master/picture/20220124151556.png)

2. 依次选择virtualenv环境，并将新环境位置定位到当前文件夹下，勾选选项可根据情况满足自身需求，确定后生成一个新的环境

![image-20220124151644477](https://gitee.com/y_kvm/img/raw/master/picture/20220124151645.png)

3. 选择在当前项目下构建的环境

![image-20220124151917753](https://gitee.com/y_kvm/img/raw/master/picture/20220124151918.png)

4. 如果项目给定了requirement.txt文件则之间参考该文件进行安装，打开该文件会提示安装环境

![image-20220124152028427](https://gitee.com/y_kvm/img/raw/master/picture/20220124152029.png)

5. 如果不存在则根据需要自行在解释器设置页面添加安装

### 在终端启动当前环境

虚拟环境创建好后，需要激活才能在当前命令行中使用，可以理解成将当前命令行环境中 PATH 变量的值替换掉

通过 virtualenv 和 模块 venv 创建的虚拟环境，激活方式是一样的，即运行激活脚本

- Windows 系统中，激活脚本路径是 `<myvenv>\Scripts\activate.bat`，如果是 powershell 命令行，脚本换成 `Activate.ps1` , 注意将 `<myvenv>` 换成你自己的虚拟环境目录

- Linux 系统中，激活脚本路径是 `<myvenv>/bin/activate`，默认脚本没有执行权限，要么设置脚本为可执行，要么用 `source` 命令执行，例如

  ```bash
  source myvenv/bin/activate
  ```

激活后终端前方会存在一个（venv）的标志。



退出虚拟环境很简单，只需要执行 `deactivate` 命令就行，这个命令也在虚拟环境的脚本目录下，因为激活时，将脚本目录设置到 PATH 中了，所以可以直接使用

退出虚拟环境相当于将 PATH 恢复成原来的

#### Win10虚拟环境报错

.\activate : 无法加载文件 H:\envproject\venv\Scripts\activate.ps1，因为在此系统上禁止运行脚本。有关详细信息，请参阅 htt

ps:/[http://go.microsoft.com/fwlink/?LinkID=135170](https://link.zhihu.com/?target=http%3A//go.microsoft.com/fwlink/%3FLinkID%3D135170) 中的 about_Execution_Policies。

所在位置 行:1 字符: 1

解决办法：

1.以管理员身份打开PowerShell

2.执行命令set-executionpolicy remotesigned

![img](https://gitee.com/y_kvm/img/raw/master/picture/20220124152816.jpeg)



## 更多的选择

- Conda
  - anaconda
  - minConda