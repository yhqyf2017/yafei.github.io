---
title: The server selected protocol version TLS10 is not accepted by client preferences [TLS12]
date: 2021-08-28 20:45:24
tags:
- Sql Server
- JDK
categories: 
- 数据库
cover: false 

---

Java连接 Sqlserver发生了报错错误如下：

```shell
The server selected protocol version TLS10 is not accepted by client preferences [TLS12] 
```

解决方法：

原因： 

​		JDK1.8高版本 不推荐使用旧的 TLSV1.0 的协议，所以默认删除 TLS10 的支持



根据环境变量配置中jre的地址，在 jre\lib\security文件夹下，编辑java.security文件
在文件中找到 jdk.tls.disabledAlgorithms配置项，将TLSv1, TLSv1.1, 3DES_EDE_CBC删除即可。

```shell
#after
jdk.tls.disabledAlgorithms=SSLv3,RC4, DES, MD5withRSA, \
    DH keySize < 1024, EC keySize < 224, anon, NULL, \
    include jdk.disabled.namedCurves
```

修改之前的如下：

```shell
#before 
jdk.tls.disabledAlgorithms=SSLv3, TLSv1, TLSv1.1, RC4, DES, MD5withRSA, \
    DH keySize < 1024, EC keySize < 224, 3DES_EDE_CBC, anon, NULL, \
    include jdk.disabled.namedCurves
```

