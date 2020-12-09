---
title: jenkins自动化部署vue项目（一）
date: 2020-12-09 09:41:19
tags: jenkins,vue
categories: 运维
keywords: jenkins
cover: false
---

## 加入nodeJS插件配置
  在插件管理中安装nodejs插件，然后在配置里面配置nodejs插件即可。
## 新建自由风格的项目

#### 基本配置（具体看图，没啥可说的）
![image.png](https://uufefile.uupt.com/PicLib/uunote/images/image_1561624238312.png)

#### 源码管理
选择项目的git地址和分支，添加上用户访问权限即可。
![image.png](https://uufefile.uupt.com/PicLib/uunote/images/image_1561624322039.png)

#### 触发器
最重要的是url和token，这两个需要在git中配置。

![image.png](https://uufefile.uupt.com/PicLib/uunote/images/image_1561624459649.png)

![image.png](https://uufefile.uupt.com/PicLib/uunote/images/image_1561624520303.png)

#### 构建环境

选择nodeJs即可。

![image.png](https://uufefile.uupt.com/PicLib/uunote/images/image_1561624549053.png)

#### 构建
执行shell命令。
```
echo $PATH

node -v
npm -v
npm install chromedriver --chromedriver_cdnurl=http://cdn.npm.taobao.org/dist/chromedriver
npm install   #安装依赖
npm run build
tar -zcvf dist.tar.gz dist/ #压缩，方便传输
```
![image.png](https://uufefile.uupt.com/PicLib/uunote/images/image_1561624597309.png)


## 配置web钩子
即刚才的url和token。

![image.png](https://uufefile.uupt.com/PicLib/uunote/images/image_1561624743878.png)

至此，自动化部署vue项目已经完成，下篇说明如何推送到远程服务器上。