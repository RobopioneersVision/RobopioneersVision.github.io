---
title: Markdown插入图片
toc: true
date: 2022-01-17 15:00:00
---

Markdown 推荐采用图片上传到图床的方式进行存储，有以下优点

- 大大减少文件体积
- 使得整体文件结构更加合理
- 避免了当前主题标题存在空格无法识别图片的问题



## 推荐方式一

**Gitee+PicGO+typora** 

### Windows 

#### 参考教程

[使用PicGo+Gitee(码云)搭建免费图床 - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1622395)

配置完成后，在typora进行设置

![image-20220117153416444](https://gitee.com/y_kvm/img/raw/master/picture/image-20220117153416444.png)

在偏好设置->图像中，设定 **上传服务设定**为PicGO(app) 并选择本地路径

设定图片上传规则，**插入图片时**上传图片为上传图片，并勾选本地和网络图片

### Ubuntu

Picgo采用官方安装的安装包如snap和appimage均存在问题,appimage在typora无法定位到可执行程序.而snap受限于自身的安全机制,无法访问npm下载插件.

对于snap可以尝试单独下载插件后在对应的安装文件夹手动安装

对于Appimage的解决方法为自定义调用指令,在typora图片上传添加自定义命令

```bash
/home/用户名/...路径.../Picgo.appimage upload
```

但是此种方法存在一个bug,只有当前picgo未启动时可以回传地址,当启动后无法作出回应将地址回传给typora,可以上传但是需要手动粘贴地址.因而还是推荐core版.

**推荐Picgo Core**



在此仅提供pcigo core命令行版教程,其余同上,在nodejs配置好后

- 安装cnpm(国内镜像加速)

```bash
sudo npm install -g cnpm
```

- 安装picgo-core

```bash
sudo cnpm install picgo -g
```

- 安装插件`picgo-plugin-gitee`

```bash
picgo install gitee
```

- 安装重命名插件super-prefix

```bash
picgo install super-prefix
```

对配置文件添加个人仓库信息设置,PicGo-core的配置文件地址：`~/.picgo/config.json`

- **ower** git用户名称必填
- **path**仓库下自定义路径,可选
- **repo**仓库名称必填
- **token**个人令牌必填

```json
{
  "picBed": {
    "current": "gitee",
    "uploader": "gitee",
    "gitee": {
      "message": "picgo commit",
      "owner": "name", 
      "path": "picture", 
      "repo": "img",	
      "token": "67e2c9994787a69ae8a66a579711fade"
    }
  },
  "picgoPlugins": {
    "picgo-plugin-gitee": true,
    "picgo-plugin-super-prefix": true
  },
  "picgo-plugin-super-prefix": {
    "fileFormat": "YYYYMMDDHHmmss"
  },
  "picgo-plugin-gitee-uploader": {
    "lastSync": "2022-01-18 10:54:06"
  }
}
```

在图片上传pcigo一栏,选择自定义指令

```bash
/usr/bin/picgo upload
```

![image-20220118110600993](https://gitee.com/y_kvm/img/raw/master/picture/20220118110601.png)

测试验证图片上传选项一切正常后即可使用
