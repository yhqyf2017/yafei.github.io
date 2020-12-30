---
title: Android11刷面具教程
date: 2020-12-29 15:33:58
tags: 
- 面具Rec 
- Android11
keywords: 
- 面具
categories: 
- Android
cover: false
---

### Android 11刷面具教程

** 本教程适用于已经是Android11（且已经解BL锁）的系统。*

**刷机前一定要备份数据**

**刷机前一定要备份数据**

**刷机前一定要备份数据**

#### 一、刷入第三方Rec

1、手机关机，同时按'音量-'和'电源键'，进入fastboot模式。

2、连接上电脑，执行以下（确保adb可行性）命令刷入：

```sh
fastboot flash recovery twrp-3.4.0-10-raphael-mauronofrio.img
```

3、刷完以后不要重启，不要重启，不要重启，返回主界面。

4、把防止加密的包拷贝到sdcard中，通过手机端刷入防止加密的包。

```sh
Disable_Dm-Verity_ForceEncrypt_11.02.2020.zip
```

5、刷完以后就可以重启了。再次进Rec可以看到data已经是解密的。

#### 二、刷入面具。

目前刷入的是21版本的（21.1版本的刷入会卡fastboot，建议刷入21.2最新版本的）

```sh
Magisk-v21.2.zip
```

刷入完重启即可。

#### 三、刷入Edxposed。

上述步骤刷完以后已经获得面具的权限。可以通过搞基助手刷入Edxposed及Edxposed Manager。



备注：

如果版本存在非Android11 的Rec，通过格式化data，然后再进行上述操作即可。

附件清单如下：

```sh
链接：https://pan.baidu.com/s/1JjhuwdKL2vb_L-BYHFS74g 
提取码：saag 
```









