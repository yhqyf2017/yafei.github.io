---
title: Oracle11g使用数据泵导入/导出数据
date: 2021-08-13 21:37:17
tags:
- Oracle
keywords: 
- Oracle
categories: 
- 数据库
---

## expdp数据导出

### 登录数据库

输入命令：sqlplus；

```sql
使用管理员角色登录需要在用户名后加“ as sysdba” 例如：sys as sysdba
```

### 创建目录路径

输入命令：

```sql
create directory data_dir as 'E:\ora\data' ; 
```

1、data_dir为路径名称，可自命名，E:\ora\data为数据库导出文件存放路径（路径必须存在）；
2、使用命令：select * from dba_directories可查询用户创建目录。

### 为oracle用户授予访问数据目录的权限

输入命令：

```sql
Grant read,write on directory data_dir to dbuser;
```

dbuser为数据库用户名

### 导入导出操作授权

输入命令：

```sql
grant exp_full_database,imp_full_database to dbuser;
```

### 退出

输入命令：

```sql
exit;
```

### 数据导出

执行命令：

```sql
expdp dbuser/123456@orcl schemas=dbuser dumpfile=expdp.dmp directory=data_dir logfile=expdp.log
```

参数说明：

expdp [为用户名]/[密码]@[服务名]
schemas=[为用户名]
dumpfile=[导出数据库文件（可自命名）]
directory=[目录名]
logfile=[日志文件文件名（可自命名）]
注意：命令结束不需要加“;”

## impdp 数据导入



### 登录数据库

输入命令：sqlplus；

```sql
使用管理员角色登录需要在用户名后加“ as sysdba” 例如：sys as sysdba
```

### 创建目录路径

输入命令：

```sql
create directory data_dir as 'E:\ora\data' ; 
```

1、data_dir为路径名称，可自命名，E:\ora\data为数据库导出文件存放路径（路径必须存在）；
2、使用命令：select * from dba_directories可查询用户创建目录。

### 为oracle用户授予访问数据目录的权限

输入命令：

```sql
Grant read,write on directory data_dir to dbuser;
```

dbuser为数据库用户名

### 导入导出操作授权

输入命令：

```sql
grant exp_full_database,imp_full_database to dbuser;
```

### 退出

输入命令：

```sql
exit;
```

### 数据导入

执行命令：

```sql
impdp user/123456@orcl REMAP_SCHEMA = dbuser:user table_exists_action = replace directory=data_dir dumpfile=expdp.dmp logfile=expdp.log
```

参数说明：

impdp [用户名]/[密码]@[服务名]
REMAP_SCHEMA=[源用户名1]:[目标用户名2]
table_exists_action=replace /*存在的表动作(覆盖)*/
directory=[目录名]
dumpfile=[.dmp文件名]
logfile=[.log文件名]

注意：命令结束不需要加“;”
