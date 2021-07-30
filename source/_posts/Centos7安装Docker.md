---
title: Centos7安装Docker
date: 2021-06-05 08:21:33
tags:
- docker
- centos
categories: 
- linux
cover: false
---

### Centos 7 安装Docker

#### 依赖配置

使用 `root` 权限登录 Centos。确保 yum 包更新到最新。

```sh
yum update
```

安装需要的软件包， yum-util 提供yum-config-manager功能，另外两个是devicemapper驱动依赖的。

```sh
yum install -y yum-utils device-mapper-persistent-data lvm2
```

设置yum源

```sh
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

#### 安装Docker

可以查看所有仓库中所有docker版本，并选择特定版本安装

```sh
yum list docker-ce --showduplicates | sort -r
```

安装Docker

```sh
yum install docker-ce  #由于repo中默认只开启stable仓库，故这里安装的是最新稳定版
yum install <FQPN>  # 例如：sudo yum install docker-ce-17.12.0.ce
```

验证是否成功

```sh
docker version
```

启动并加入开机启动

```sh
systemctl start docker
systemctl enable docker
```



### 安装可视化界面Portainer

安装Docker之后，运行以下命令以获取最新的Portainer映像。

```sh
docker search portainer
```

拉取镜像

```sh
docker pull portainer/portainer
```

示例如下：

```sh
[root@localhost ~]# docker pull portainer/portainer
Using default tag: latest
latest: Pulling from portainer/portainer
94cfa856b2b1: Pull complete 
49d59ee0881a: Pull complete 
a2300fd28637: Pull complete 
Digest: sha256:fb45b43738646048a0a0cc74fcee2865b69efde857e710126084ee5de9be0f3f
Status: Downloaded newer image for portainer/portainer:latest
docker.io/portainer/portainer:latest
```

查看镜像

```sh
docker images
```

启动

```sh
docker run -d -p 9000:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --name prtainer-test portainer/portainer
```

访问

```sh
http://IP:9000
```





备注：

卸载Docker命令

 yum erase+Docker版本号，例如如下：

```sh
 yum erase docker-common-2:1.12.6-68.gitec8512b.el7.centos.x86_64
```
