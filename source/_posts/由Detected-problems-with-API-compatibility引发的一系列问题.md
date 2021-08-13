---
title: 由Detected problems with API compatibility引发的一系列问题
date: 2018-12-06 22:12:02
tags:
- Android
keywords: 
- Android
categories: 
- Android
---

## 问题重现

由于将安卓版本升级到了9.0，每次调试都会弹出Detected problems with API compatibility(visit g.co/dev/appcompat for more info)，如下图所示：

![img](https://img2018.cnblogs.com/blog/1171252/201812/1171252-20181206100550215-1741372076.jpg)

经查百度得知，原来是调用了安卓隐藏的API，才会出现这个问题。

## 解决方案

当时采取了官方的建议，在如下位置加入targetSDKversion，版本28，也就是Android 9.0，即：

```java
"google":{
  "targetSdkVersion":28,
}
```

改成这个以后，打包APP，网络访问都没有，直接网络访问错误，也就是所有的请求都无法正常使用。

其实这个问题是勾选debug调试引起的，只要打包时不勾选debug模式，就不会出现这个问题。（应该是debug模式中调用的隐藏的API）。

## 引发的问题

下一次打包时把这个配置给去掉了，再次安装应用时就会安装失败，提示（权限版本无法降级（-26），小米的提示，其他机型也都是安装失败）：

问题所在就是上个版本调用的API是29的，而升级的版本调用的API是23的，所以才会安装失败。也就是SDK版本从低版本升级到高版本是可以的，而反过来就不行。

-------------------以前的问题，现从博客园迁移过来

