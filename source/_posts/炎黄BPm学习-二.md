---
title: 炎黄BPM学习(二)
date: 2021-06-11 09:17:45
tags:
- 炎黄BPM
- BPM
categories: 
- BPM
cover: false
---

### BPM定时器

#### 支持的类型

- Job，常规定时任务
- SOAP Job，定时访问SOAP Web服务
- HTTP Job，定时访问HTTP(s) Web服务
- SQL Job，定时执行数据库SQL

#### 创建常规job

在指定的触发条件执行一次或周期重复执行指定的Java程序。

##### 开发步骤

###### 实现IJob接口

**示例**

```java
package com.actionsoft.apps.poc.api.local.job;

import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;

import com.actionsoft.bpms.schedule.IJob;
import com.actionsoft.sdk.local.SDK;

public class HelloJob implements IJob {

    public void execute(JobExecutionContext jobExecutionContext)
        throws JobExecutionException {
        // 读管理员配置的扩展参数串，支持简单的@公式
        String param = SDK.getJobAPI().getJobParameter(jobExecutionContext);
        System.out.println("Hello AWS PaaS Job Demo! Param = " + param);
    }
}
```

- SDK.getJobAPI().getJobParameter 读取维护的扩展参数
- SDK.getJobAPI().getJobModel 读取Job配置模型

任务的配置信息将创建在选择的应用。同时，您编译的Java代码必须部署在该应用下，即%AWS-HOME%/apps/install/%your appId%/lib

###### 注册

1. 登录您的AWS PaaS实例控制台
2. 访问“调度服务”页面
3. 点击“新建”按钮
4. 输入Java程序所在的应用，并选择“Job 常规定时任务”
5. 点击“确定”按钮，完成新建

###### 配置

| 项         | 说明                                                         |
| :--------- | :----------------------------------------------------------- |
| 任务名称   | 易于运维人员了解的Job名，如`异常交易监控`                    |
| 任务执行类 | Java类路径，请[移步到这里](https://docs.awspaas.com/reference-guide/aws-paas-job-reference-guide/job_dev/sample_job.html) |
| 顺序执行   | 选中后，上下文参数被持久化，Job不会并行执行。等同于[互斥Job](https://docs.awspaas.com/reference-guide/aws-paas-job-reference-guide/job_dev/concurrently_job.html) |
| 自定义参数 | 自定义的串（可选），开发者可以在程序中读取该值，支持基本的@公式 |
| 触发规则   | 执行该Job的循环策略                                          |
| 通知规则   | 执行该Job的通知规则，支持手机短信、邮件通知、企业微信、钉钉、飞书通知，详见[邮件通知-系统通知](https://docs.awspaas.com/apps/com.actionsoft.apps.addons.mail/appendix/scenes.html#a) |

#### 创建SOAP Job

在指定的触发条件执行一次或周期重复执行指定的SOAP服务，该服务指向一个AWS CC的SOAP适配器。

##### 开发步骤

###### CC注册SOAP适配器

1. 登录您的AWS PaaS实例控制台
2. 访问“连接服务”页面
3. 点击“新建”按钮
4. 选择“访问SOAP Web服务”
5. 点击“确定”按钮，完成新建

###### 新建Job

1. 访问“调度服务”页面
2. 点击“新建”按钮
3. 选择“SOAP Job // 定时访问SOAP Web服务”
4. 点击“确定”按钮，完成新建

###### 配置

| 项       | 说明                                                         |
| :------- | :----------------------------------------------------------- |
| 任务名称 | 易于运维人员了解的Job名，如`定时批量更新ERP付款状态`         |
| CC SOAP  | 访问列表，由AWS CC提供的SOAP技术适配器                       |
| 调用条件 | true/false值，当值为false时不执行（可选）。支持基本的@公式   |
| 请求参数 | 一个JSON串（可选），key/value对应该SOAP服务的方法参数和值。支持基本的@公式，如`{"param1":"来自SOAP Job的调度请求 - @date"}` |
| 触发规则 | 执行该Job的循环策略                                          |
| 通知规则 | 执行该Job的通知规则，支持邮件通知、企业微信、钉钉通知，详见[邮件通知](https://docs.awspaas.com/apps/com.actionsoft.apps.addons.mail/appendix/scenes.html#a) |

#### 创建HTTP Job

在指定的触发条件执行一次或周期重复执行指定的HTTP服务，该服务指向一个AWS CC的HTTP适配器。

##### 开发步骤

###### CC，注册HTTP适配器

1. 登录您的AWS PaaS实例控制台
2. 访问“连接服务”页面
3. 点击“新建”按钮
4. 选择“访问Http(s) Web服务”
5. 点击“确定”按钮，完成新建。

###### 新建Job

1. 访问“调度服务”页面
2. 点击“新建”按钮
3. 选择“HTTP Job // 定时访问Http(s) Web服务”
4. 点击“确定”按钮，完成新建

###### 配置

| 项       | 说明                                                         |
| :------- | :----------------------------------------------------------- |
| 任务名称 | 易于运维人员了解的Job名                                      |
| CC HTTP  | 访问列表，由AWS CC提供的HTTP技术适配器                       |
| 调用条件 | true/false值，当值为false时不执行（可选）。支持基本的@公式   |
| 请求参数 | 一个JSON串（可选），key/value对应该URL的参数和值。支持基本的@公式，如`{"msg":"来自HTTP Job的调度请求 - @date"}` |
| 触发规则 | 执行该Job的循环策略                                          |
| 通知规则 | 执行该Job的通知规则，支持邮件通知、企业微信、钉钉通知，详见[邮件通知](https://docs.awspaas.com/apps/com.actionsoft.apps.addons.mail/appendix/scenes.html#a) |

#### 创建SQL Job

在指定的触发条件执行一次或周期重复执行指定的数据库SQL。

##### 开发步骤

###### 新建Job

1. 访问“调度服务”页面
2. 点击“新建”按钮
3. 选择“SQL Job // 定时执行数据库SQL”
4. 点击“确定”按钮，完成新建

###### 配置

| 项       | 说明                                                         |
| :------- | :----------------------------------------------------------- |
| 任务名称 | 易于运维人员了解的Job名，如`转移60天后的日志数据`            |
| DB连接   | 数据源列表，由AWS CC提供的JDBC技术适配器                     |
| 开启事务 | SQL操作要么全部成功，要么全部回滚                            |
| SQL      | SELECT/UPDATE/DELETE语句，多个按顺序执行，支持简单@公式      |
| 触发规则 | 执行该Job的循环策略                                          |
| 通知规则 | 执行该Job的通知规则，支持邮件通知、企业微信、钉钉通知，详见[邮件通知](https://docs.awspaas.com/apps/com.actionsoft.apps.addons.mail/appendix/scenes.html#a) |

