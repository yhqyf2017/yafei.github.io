---
title: NCCloud简单表格分页
date: 2021-09-14 21:56:36
tags:
- NCCloud
- 前端高阶组件
keywords: 
- NCCloud
- 分页
categories: 
- NCCloud
cover: false
---

NCCloud生成的主子表（列表页面）默认是没有开启分页的，需要在数据库中手动开启，代码不需要修改，修改完成之后重启即可。

需要修改的表：```PUB_AREA```

需要修改的字段：```pagination```

需要根据模板Id去查询是否分页：

```sql
select * from PUB_AREA where pk_area='0001ZZZZfe6e1b8f6341'
```

重点看```pagination``` ，默认是没有开启分页的，需要改成true。

```sql
update PUB_AREA set pagination='true' where pk_area='0001ZZZZfe6e1b8f6341'
```



修改完成之后重启服务即可。
