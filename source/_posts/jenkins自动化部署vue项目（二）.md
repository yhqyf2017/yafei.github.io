---
title: jenkins自动化部署vue项目（二）
date: 2020-12-09 10:20:11
tags:jenkins,vue
---

##  安装插件Publish Over SSH
安装Publish Over SSH插件，并陪系统设置里面配置ssh的hostname、url、username等相关信息，如下图所示：

![image.png](https://uufefile.uupt.com/PicLib/uunote/images/image_1561625203366.png)

## 配置 Post-build Actions

英文名字应该都懂，在这里也不多说。

同样也需要执行命令

```
cd /root/server/vue   #进入目录

tar -zxvf aic.tar.gz         #解压传输过来的代码包

rm -rf aic.tar.gz      #删除代码包

```
![image.png](https://uufefile.uupt.com/PicLib/uunote/images/image_1561625338111.png)

至此，就就已经完成。