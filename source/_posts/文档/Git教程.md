---
title:  Git 教程
toc: true
date: 2021-10-07 01:31:20
---

## 在线沙盒式可视化学习
[Learn Git Branching](https://learngitbranching.js.org/?locale=zh_CN)

## 基础知识


## 工具

### GitKraken



## 创建本地git并推送到远程仓库

### 1. 创建本地git管理系统

以Jetbrain系列为例，点击上方菜单VCS->启动版本控制集成（VCS->Enable Version Control Intergration

![image-20220108210814119](Git教程/image-20220108210814119.png)

### 2.  添加git追踪文件

1. 选中所有需要提交的项目
2. 右键点击，在菜单中选择git->Add,将目标文件进行追踪
3. 点击commit进行提交，编写合适的说明文字后提交到本地

### 3. 创建网络代码仓库

新建一个代码仓库，英文命名单词首字母大写

![image-20220108211517917](Git教程/image-20220108211517917.png)

在描述中写明仓库内容和作者，不生成README和gitignore，选择公开仓库（否则别人下载需要你的帐号密码）完成创建。

![image-20220108211627213](Git教程/image-20220108211627213.png)

### 4. 本地推送

点击上方的GIt（原来的VCS）->管理远程添加远程仓库

![image-20220108210814119](Git教程/image-20220108210814119.png)

复制远程仓库链接，添加到本地的远程仓库，点击Push完成推送

![image-20220108212059394](Git教程/image-20220108212059394.png)





## Git 进阶使用技巧

### 1.cherry pick

切换分支，然后在另一个分支上的某个commit上右击，选择cherry pick就可以把该commit，提交到当前分支。



![img](https://gitee.com/y_kvm/img/raw/master/picture/202201171615521.webp)

### 2.stash

> stash(贮藏)、pop(释放-将准备好的动心突然拿出来)
>  **使用场景：**
>  在实际开发中，解决Bug是避免不了的，每个Bug分支都是新建一个临时分支来修复的，修复完成后合并分支，删除临时分支
>  当在develop分支上开发新功能，代码写到一半时，突然测服报了一个bug要现在解决
>  功能写到一半总不能现在提交，解决Bug在新的分支上，要保持工作区和暂存区是干净的，stash就派上了用场

gitKraken上的操作：
 功能开发分支(当前分支)，点击上方菜单的Stash，可以看到工作区和暂存区都干干净净的，log区域会有个存储样式的图标
 然后去处理Bug，Bug处理完成之后，切回到功能分支
 点击pop，将储存的代码释放出来，继续开发

![img](https://gitee.com/y_kvm/img/raw/master/picture/202201171616238.webp)



![img](https://gitee.com/y_kvm/img/raw/master/picture/202201171616774.webp)



### 3.合并多个commit

有时提交了很多commit，比如有1，2，3，共三个commit，这三个commit都是为了实现某个功能而做的更改。那么我们可以把这三个commit合并成一个再进行提交。
 操作，右击第一个commit的前一个提交，选择reset to this commit -> soft, 这样就会把1，2，3，三个commit的更改合并在一起，然后再把这些更改重新提交一次即可。

### 4. submodule

对于不需要进行修改直接调用的库我们可以使用submodule进行管理

```bash
git clone <repository> --recursive  //递归的方式克隆整个项目
git submodule add <repository> <path> //添加子模块
git submodule init //初始化子模块
git submodule update //更新子模块
git submodule foreach git pull  //拉取所有子模块
```



## 参考资料

[git submodule 完整用法整理 ](https://www.cnblogs.com/zhoug2020/p/13544721.html)

[git - gitKraken可视化工具（二） - 简书 (jianshu.com)](https://www.jianshu.com/p/a8e448f13754)