---
title: yShop环境部署
date: 2021-01-09 22:37:54
tags: Java
keywords: 
- yShop
- Java
categories: 
- Java
---

## 环境部署

### JDK部署

#### 下载

		https://www.oracle.com/cn/java/technologies/javase/javase-jdk8-downloads.html

#### 安装

	省略

#### 配置

（1）系统变量：```JAVA_HOME```

新建用户变量，```JAVA_HOME```，变量值为安装后的jdk的绝对路径，此处为：D:\java\jdk1.8.0

（2）系统变量：```CLASSPATH```

```shell
 .;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar
```

（需要注意的是变量值前边的.;一定不能少）

（3）用户变量：path（后边加）

```shell
;%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin（加在尾部）
```

#### 验证

```java
java -version
```



### maven部署

#### 下载

		https://maven.apache.org/download.cgi

#### 安装
​	下载完成之后解压
​	把maven放到一个目录下

#### 配置

		打开绝对路径+/apache-maven-3.8.1\conf    配置settings.xml

配置本地仓库目录(D:\Program Files\maven-repository是本地仓库路径)

```shell
 <localRepository>D:\Program Files\maven-repository</localRepository>
```

配置中央仓库

在```<mirrors></<mirrors>```里边加入中央仓库地址，例如阿里云中央仓库地址

```java
<mirror>
   <id>alimaven</id>
   <mirrorOf>maven</mirrorOf>
   <name>aliyun maven</name>
   <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
 </mirror>
```

加入环境变量

配置MAVEN_HOME   为maven的路径，例如：

```
D:\Program Files\apache-maven-3.8.1
```

用户变量：path（后边加）

```shell
%MAVEN_HOME%\bin
```

#### 验证

```
mvn -version
```

### 安装数据库（mysql）

下载地址

```
https://dev.mysql.com/downloads/windows/installer/8.0.html
```

注意安装过程中配置的数据库，密码。

下载完成之后安装即可。

### 安装Redis

​	详见链接 ：https://www.cnblogs.com/cang12138/p/8880776.html

### 后端项目编译

	配置数据库连接 D:\example\yshopmall\yshop-admin\src\main\resources\config      编辑  application-dev.yml

```java
driverClassName: com.mysql.cj.jdbc.Driver
url: jdbc:mysql://localhost:3306/yshop?serverTimezone=Asia/Shanghai&characterEncoding=utf8&useSSL=false&zeroDateTimeBehavior=convertToNull&allowPublicKeyRetrieval=true
username: root
password: 123456
```

进入项目根目录下执行

```
mvn install
```

jar包生成

```
D:\example\yshopmall\yshop-admin\target    包文件:yshop-admin-2.3.jar
```

启动：

```
java -jar yshop-admin-2.3.jar
```

### 前端项目编译

前端项目下载地址:

```sh
https://gitee.com/guchengwuyue/yshopmall_qd
```

安装nodeJs,下载地址：

```
https://nodejs.org/dist/v8.3.0/
```

```
node-v8.3.0-x64.msi    
```

编译前端项目：根目录下执行：

```
npm install
```

安装完成之后：

开发环境启动：

```
npm run dev
```



生产环境打包：

```
npm run build
```

会在根目录下生成dist文件夹，下边的文件需要放到nginx下运行。
