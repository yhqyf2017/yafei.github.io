---
title: 炎黄BPm学习(一)
date: 2021-06-10 17:03:15
tags:
- 炎黄BPM
- BPM
categories: 
- BPM
cover: false
---

### 一、环境搭建

1. 手动将` %AWS_HOME%/db_script `目录下对应数据库脚本文件初始化到您已准备好的的AWS数据库里
2. 使用`%AWS_HOME%/bin/passwd.bat` 文件，生成AWS数据库密码的加密密码
3. 修改`%AWS_HOME%/bin/conf/server.xml `文件中数据库连接信息<database/>，其中密码为步骤2生成的加密密
4. 执行`%AWS_HOME%/bin/httpd-startup.sh`  和  `aws_startup.sh (linux)`,`startup(windows)`启动AWS服务
5. 访问地址： `http://localhost:8088/portal/console`   用户名admin  密码 1

### 二、许可申请

#### 获取机器码

1. 打开安装目录下的bin文件夹找到licensekey文件。
2. 打开licensekey文件（Windows运行.bat文件；Linux打开.sh文件）。
3. 请输入想要申请授权的[IP地址](#_如何获得IP地址)，回车。中间部分即为机器码。
4. 把获得的机器码，复制发送给炎黄盈动的人员，重新申请新的软件授权许可文件(license)。

#### 获得授权文件

将得到的license文件，复制到安装目录下的bin文件中，重启服务器就可以了。

### 三、创建和分发应用

注意授权即可。其他的详见官方文档。

### 四、设计部署流程

简单流程： 创建应用-》创建流程-》测试流程-》部署流程-》访问流程

带表单流程：创建应用-》创建流程-》测试流程-》部署流程-》访问流程-》创建表单（创建BO、创建表单、绑定到流程、测试表单

​	创建表单流程： 创建存储模型-》创建表单模型

​	*使用@公式，将表单数据提取到流程和任务标题上*

​	eg. @form(BO_EU_SUPPORT,NO)-@form(BO_EU_SUPPORT,QTITLE)

### 五、部署DW数据管理

数据视图

### 六、部署方案

[本地私有部署](https://docs.awspaas.com/help/install/#a)、[云环境部署](https://docs.awspaas.com/help/install/#f)、[Docker+Kubernetes容器部署](https://docs.awspaas.com/help/install/#e)

### 七、开发指南

#### 7.1、开发环境搭建，idea环境搭建。

  导入导入AWS PaaS平台 bin/patch(如有)、bin/jdbc、bin/lib 目录及其子目录内所有jar包。

- 配置启动类为`aws-infrastructure-common.jar`的`StartUp`
- Working directory 目录为 `%AWS_HOME%/bin`

如图所示![小Q截图-20210610115055](C:\Users\Administrator\Desktop\小Q书桌-截图\小Q截图-20210610115055.png)



最后手动执行%AWS_HOME%/bin/httpd-startup.bat(sh)脚本启动AWS WEB服务。

#### 7.2、开发流程事件

- 继承父类（通常不同类型的事件提供不同的父类），基于[接口编程](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/introduction/interface.html)
- 注册到指定的流程中，等待触发

##### 1、开发步骤

- 继承事件[编程接口](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/introduction/interface.html)，完成代码开发
- 将jar打包到该App的资源目录（install/%appId%/lib）
- 选择事件类型之后，自动查找该类型对应的实现类
- 测试流程执行，在IDE调试代码
- 注册事件【人工任务】人工任务属性
- 事件触发器显示java规则
  - 根据类中继承的抽象类进行匹配
  - 下拉列表的展示内容：类名，描述，版本号，开发商
  - 读取类的成员变量信息，自动生成扩展属性。key值格式：事件名_类名_成员变量名
  - 运行时刻使用填写的值给成员变量赋值
  - 支持已经注册的类的刷新动作，用于成员变量有变化时初始化扩展参数。

**成员变量**

- 事件触发器类提供成员变量的定义，运行时刻，会使用对应的值进行初始化，目前支持类型：String、Date、Integer、Long、Double、Boolean

**编程接口** 

| 接口                       | 说明                                                         |
| :------------------------- | :----------------------------------------------------------- |
| InterruptListenerInterface | -返回Boolean，中断操作 - 开发者继承**InterruptListener**抽象类 |
| ValueListenerInterface     | -返回String，取值操作 -开发者继承**ValueListener**抽象类     |
| ExecuteListenerInterface   | -逻辑处理 -开发者继承**ExecuteListener**抽象类               |

```java
package com.actionsoft.apps.poc.api.local.process.listener.process;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.InterruptListener;

public class Test_PROCESS_BEFORE_COMPLETED extends InterruptListener {

    public String getDescription() {
        return "测试用例";
    }

    public boolean execute(ProcessExecutionContext ctx) throws Exception {
        info("流程完成前事件被触发-->" + ctx.getProcessInstance());
        return true;
    }

}
```

##### 2、事件清单

###### Process Event

**流程通用事件**

无论是人工流程还是系统短流程，都被触发的事件

| 事件名称                                                     | 说明       |
| :----------------------------------------------------------- | :--------- |
| <a href="#PROCESS_BEFORE_CREATE">PROCESS_BEFORE_CREATE</a>   | 流程创建前 |
| <a href="#PROCESS_AFTER_CREATE">PROCESS_AFTER_CREATE</a>     | 流程创建后 |
| <a href="#PROCESS_START">PROCESS_START</a>                   | 流程启动   |
| <a href="#PROCESS_SUSPEND">PROCESS_SUSPEND</a>               | 流程挂起   |
| <a href="#PROCESS_RESUME">PROCESS_RESUME</a>                 | 流程恢复   |
| <a href="#PROCESS_BEFORE_COMPLETE">PROCESS_BEFORE_COMPLETE</a> | 流程完成前 |
| <a href="#PROCESS_AFTER_COMPLETE">PROCESS_AFTER_COMPLETE</a> | 流程完成后 |
| <a href="#PROCESS_BEFORE_TERMINATE">PROCESS_BEFORE_TERMINATE</a> | 流程终止前 |
| <a href="#PROCESS_AFTER_TERMINATE">PROCESS_AFTER_TERMINATE</a> | 流程终止后 |
| <a href="#PROCESS_BEFORE_CANCEL">PROCESS_BEFORE_CANCEL</a>   | 流程取消前 |
| <a href="#PROCESS_AFTER_CANCEL">PROCESS_AFTER_CANCEL</a>     | 流程取消后 |
| <a href="#PROCESS_BEFORE_DELETE">PROCESS_BEFORE_DELETE</a>   | 流程删除前 |
| <a href="#PROCESS_AFTER_DELETE">PROCESS_AFTER_DELETE</a>     | 流程删除后 |
| <a href="#PROCESS_BEFORE_REACTIVATE">PROCESS_BEFORE_REACTIVATE</a> | 流程复活前 |
| <a href="#PROCESS_AFTER_REACTIVATE">PROCESS_AFTER_REACTIVATE</a> | 流程复活后 |

**人工流程专有事件**

含有人工节点的流程范围内全局事件

| 事件名称                                                     | 说明                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| <a href="#PROCESS_ACTIVITY_ADHOC_BRANCH">PROCESS_ACTIVITY_ADHOC_BRANCH</a> | 程序指定后继路线和参与者。如果节点定义了ACTIVITY_ADHOC_BRANCH事件，则优先应用节点的实现 |
| <a href="#PROCESS_FORM_GRID_FILTER">PROCESS_FORM_GRID_FILTER</a> | 全局表单子表过滤。如果节点定义了FORM_GRID_FILTER事件，则优先应用节点实现 |
| <a href="#PROCESS_FORM_BEFORE_LOAD">PROCESS_FORM_BEFORE_LOAD</a> | 全局表单加载前事件。如果节点定义了FORM_BEFORE_LOAD事件，则优先应用节点实现 |
| <a href="#PROCESS_FORM_AFTER_LOAD">PROCESS_FORM_AFTER_LOAD</a> | 全局表单加载后事件。如果节点定义了FORM_AFTER_LOAD事件，则优先应用节点实现 |

**子流程专有事件**

含有子流程节点的流程范围内全局事件

| 事件名称                                                     | 说明                       |
| :----------------------------------------------------------- | :------------------------- |
| [CALLACTIVITY_BEFORE_SUBPROCESS_START](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/callactivity_event/callactivity_before_subprocess_start.html) | 子流程实例创建后启动前事件 |
| [CALLACTIVITY_AFTER_SUBPROCESS_COMPLETE](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/callactivity_event/callactivity_after_subprocess_complete.html) | 子流程实例结束后事件       |

###### Activity Event

**节点通用事件**

流程中各种节点都会触发的事件

| 事件名称                                                     | 说明       |
| :----------------------------------------------------------- | :--------- |
| [TASK_BEFORE_COMPLETE](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/activity_event/task_before_complete.html) | 任务完成前 |
| [TASK_AFTER_COMPLETE](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/activity_event/task_after_complete.html) | 任务完成后 |
| [TASK_SUSPEND](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/activity_event/task_suspend.html) | 任务挂起   |
| [TASK_RESUME](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/activity_event/task_resume.html) | 任务恢复   |
| [ACTIVITY_BEFORE_LEAVE](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/activity_event/activity_before_leave.html) | 节点离开前 |
| [ACTIVITY_AFTER_LEAVE](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/activity_event/activity_after_leave.html) | 节点离开后 |

**UserTask节点专有事件**

当该节点为“UserTask”类型时，以下事件被触发

| 事件名称                                                     | 说明                       |
| :----------------------------------------------------------- | :------------------------- |
| [ACTIVITY_ADHOC_BRANCH](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/usertask_event/activity_adhoc_branch.html) | 程序指定后继路线和参与者   |
| [ACTIVITY_CONFIRM_PARTICIPANTS](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/usertask_event/activity_confirm_participants.html) | 节点就绪参与者即将产生任务 |
| [TASK_BEFORE_UNDO](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/usertask_event/task_before_undo.html) | 任务收回前被触发           |
| [TASK_AFTER_UNDO](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/usertask_event/task_after_undo.html) | 任务收回后被触发           |
| [FORM_COMPLETE_VALIDATE](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/form_event/form_complete_validate.html) | 流程表单办理前校验         |
| [FORM_TOOLBAR_BUILD](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/form_event/form_toolbar_build.html) | 表单工具栏构建             |
| [FORM_BEFORE_LOAD](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/form_event/form_before_load.html) | 表单加载前                 |
| [FORM_AFTER_LOAD](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/form_event/form_after_load.html) | 表单加载后                 |
| [FORM_BEFORE_SAVE](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/form_event/form_before_save.html) | 表单保存前                 |
| [FORM_AFTER_SAVE](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/form_event/form_after_save.html) | 表单保存后                 |
| [FORM_BEFORE_REMOVE](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/form_event/form_before_remove.html) | 表单删除前                 |
| [FORM_AFTER_REMOVE](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/form_event/form_after_remove.html) | 表单删除后                 |
| [FORM_GRID_FILTER](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/form_event/form_grid_filter.html) | 表单子表过滤               |
| [FORM_GRID_EXCEL_TRANSFORM](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/form_event/form_grid_excel_transform.html) | 表单子表Excel转换处理      |

###### 全局事件

以下事件被引擎全局捕获

| 事件名称      | 说明       |
| :------------ | :--------- |
| TASK_CREATE   | 任务创建后 |
| TASK_READ     | 任务阅读后 |
| TASK_COMPLETE | 任务执行完 |
| TASK_DELETE   | 任务删除后 |
| TASK_SUSPEND  | 任务挂起后 |
| TASK_RESUME   | 任务恢复后 |

##### 3、上下文对象-ProcessExecutionContext

###### ctx常用方法

- getProcessInstance 获得当前流程实例对象
- getTaskInstance 获得当前任务实例对象
- getUserContext 获得当前用户上下文对象
- getVariable 读取指定的流程变量
- isChoiceActionMenu 当前人工任务是否选中了指定的审核菜单
- execAtScript 执行@公式脚本
- addFormReadOnlyPolicy 程序指定BO操作只读（优先级最高）
- addFormEditablePolicy 程序指定BO可编辑（优先级最高）
- addFormHiddenPolicy 程序指定BO字段隐藏（优先级最高）
- addFormDisplayPolicy 程序指定BO字段显示（优先级最高）
- addGridHiddenPolicy 程序指定子表列的BO字段隐藏（优先级最高）
- addGridDisplayPolicy 程序指定子表列的BO字段显示（优先级最高）
- addFormNotNullPolicy 程序指定BO字段必填（优先级最高）
- addFormNullablePolicy 程序指定BO字段选填（优先级最高）
- addGridColumnPolicy 程序指定子表列头的字段信息（可控制显示顺序，优先级最高，高于子表列字段的显示隐藏策略）

##### 4、常用SDKAPI

- DBSql 本地数据库操作
- SDK.getBOAPI BO表读、写、查操作
- SDK.getRuleAPI 执行规则脚本
- SDK.getCCAPI 执行CC连接
- SDK.getProcessAPI 流程实例控制
- SDK.getProcessQueryAPI 流程实例查询
- SDK.getTaskAPI 任务实例控制
- SDK.getTaskQueryAPI 任务查询
- SDK.getHistoryTaskQueryAPI 历史任务查询
- SDK.getAppAPI 应用API，如执行ASLP
- SDK.getORGAPI 组织结构查询、处理
- SDK.getFormAPI 表单接口

 ##### 5、流程事件

###### [PROCESS_BEFORE_CREATE](#PROCESS_BEFORE_CREATE)

**流程创建前被触发**

| 项     | 说明                                                         |
| :----- | :----------------------------------------------------------- |
| 抽象类 | InterruptListener                                            |
| 接口   | InterruptListenerInterface                                   |
| 返回值 | 返回false，流程创建被阻止                                    |
| 异常   | -如抛出异常时，异常被包装成结果返回，流程创建失败 -其中抛出BPMNError异常时，若该流程为子流程可被父流程CallActivity定义的 边界错误事件捕获。如未定义，业务错误信息包装成结果返回 |

**新建流程**

```java
//创建流程实例
ProcessInstance processInst = SDK.getProcessAPI().createProcessInstance(processDefId, processBusinessKey,uid,createUserDeptId,createUserRoleId,title,vars);
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.process;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.InterruptListener;
import com.actionsoft.exception.BPMNError;

public class Test_PROCESS_BEFORE_CREATED extends InterruptListener {

    public String getDescription() {
        return "测试用例";
    }

    public boolean execute(ProcessExecutionContext ctx) throws Exception {
        info("流程创建前事件被触发");
        String str1 = ctx.getParameterOfString("str1");// 从临时变量中获得，在创建前尚未保存流程变量值
        if (str1 == null || str1.equals("begin")) {
            // 模拟抛出业务异常
            throw new BPMNError("BIZ001", "PROCESS_BEFORE_CREATED事件模拟抛出业务异常");
        } else {
            return true;
        }
    }
}
```

###### [PROCESS_AFTER_CREATE](#PROCESS_AFTER_CREATE)

**流程创建后被触发**

| 项     | 说明                                                         |
| :----- | :----------------------------------------------------------- |
| 抽象类 | ExecuteListener                                              |
| 接口   | ExecuteListenerInterface                                     |
| 返回值 | 无                                                           |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 -其中抛出BPMNError异常时，若该流程为子流程可被父流程CallActivity定义的 边界错误事件捕获。如未定义，业务错误信息包装成结果返回 |

**新建流程**

```java
//创建流程实例
ProcessInstance processInst = SDK.getProcessAPI().createProcessInstance(processDefId, processBusinessKey,uid,createUserDeptId,createUserRoleId,title,vars);
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.process;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListener;

public class Test_PROCESS_AFTER_CREATED extends ExecuteListener {

    public String getDescription() {
        return "测试用例";
    }

    public void execute(ProcessExecutionContext ctx) throws Exception {
        info("流程创建后事件被触发-->" + ctx.getProcessInstance());
    }

}
```

###### [PROCESS_START](#PROCESS_START)

**流程启动后被触发**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | ExecuteListener                                     |
| 接口   | ExecuteListenerInterface                            |
| 返回值 | 无                                                  |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

**启动流程**

```java
//创建流程实例
ProcessInstance processInst = SDK.getProcessAPI().createProcessInstance(processDefId, processBusinessKey,uid,createUserDeptId,createUserRoleId,title,vars);
//从默认的开始事件启动流程
List<TaskInstance> tasks = SDK.getProcessAPI().start(processInst, null).fetchActiveTasks();
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.process;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListener;

public class Test_PROCESS_START extends ExecuteListener {

    public String getDescription() {
        return "测试用例";
    }

    public void execute(ProcessExecutionContext ctx) throws Exception {
        info("流程启动后事件被触发-->" + ctx.getProcessExecutionTracks());
    }

}
```

###### [PROCESS_SUSPEND](#PROCESS_SUSPEND)

**流程挂起后被触发**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | ExecuteListener                                     |
| 接口   | ExecuteListenerInterface                            |
| 返回值 | 无                                                  |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

**流程挂起**

```java
//创建流程实例
ProcessInstance processInst = SDK.getProcessAPI().createProcessInstance(processDefId, processBusinessKey,uid,createUserDeptId,createUserRoleId,title,vars);
//从默认的开始事件启动流程
List<TaskInstance> tasks = SDK.getProcessAPI().start(processInst, null).fetchActiveTasks();
//挂起流程实例
SDK.getProcessAPI().suspend(processInst);
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.process;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListener;

public class Test_PROCESS_SUSPEND extends ExecuteListener {

    public String getDescription() {
        return "测试用例";
    }

    public void execute(ProcessExecutionContext ctx) throws Exception {
        info("流程挂起后事件被触发-->" + ctx.getProcessInstance());
    }
}
```

###### [PROCESS_RESUME](#PROCESS_RESUME)

**流程恢复后被触发**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | ExecuteListener                                     |
| 接口   | ExecuteListenerInterface                            |
| 返回值 | 无                                                  |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

**流程恢复**

```java
//创建流程实例
ProcessInstance processInst = SDK.getProcessAPI().createProcessInstance(processDefId, processBusinessKey,uid,createUserDeptId,createUserRoleId,title,vars);
//从默认的开始事件启动流程
List<TaskInstance> tasks = SDK.getProcessAPI().start(processInst, null).fetchActiveTasks();
//恢复流程实例
SDK.getProcessAPI().resume(processInst);
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.process;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListener;

public class Test_PROCESS_RESUME extends ExecuteListener {

    public String getDescription() {
        return "测试用例";
    }

    public void execute(ProcessExecutionContext ctx) throws Exception {
        info("流程恢复后事件被触发-->" + ctx.getProcessInstance());
    }

}
```

###### [PROCESS_BEFORE_COMPLETE](#PROCESS_BEFORE_COMPLETE)

**流程完成（结束）前被触发**

| 项     | 说明                                                         |
| :----- | :----------------------------------------------------------- |
| 抽象类 | InterruptListener                                            |
| 接口   | InterruptListenerInterface                                   |
| 返回值 | 返回false，流程结束被阻止，该分支被孤立中断                  |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 -其中抛出BPMNError异常时，若该流程为子流程可被父流程CallActivity定义的 边界错误事件捕获。如未定义，业务错误信息包装成结果返回 |

**流程结束**

```java
//创建流程实例
ProcessInstance processInst = SDK.getProcessAPI().createProcessInstance(processDefId, processBusinessKey,uid,createUserDeptId,createUserRoleId,title,vars);
//从默认的开始事件启动流程
List<TaskInstance> tasks = SDK.getProcessAPI().start(processInst, null).fetchActiveTasks();

//自然结束流程实例时（最后一个任务）
SDK.getTaskAPI().completeTask(tasks.get(0), UserContext.fromUID(startUser), true);

//或者终止一个流程实例时
SDK.getProcessAPI().terminate(processInst,UserContext.fromUID(terminateUser));
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.process;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.InterruptListener;

public class Test_PROCESS_BEFORE_COMPLETED extends InterruptListener {

    public String getDescription() {
        return "测试用例";
    }

    public boolean execute(ProcessExecutionContext ctx) throws Exception {
        info("流程完成前事件被触发-->" + ctx.getProcessInstance());
        return true;
    }

}
```

###### [PROCESS_AFTER_COMPLETE](#PROCESS_AFTER_COMPLETE)

**流程完成（结束）后被触发**

| 项     | 说明                                                         |
| :----- | :----------------------------------------------------------- |
| 抽象类 | ExecuteListener                                              |
| 接口   | ExecuteListenerInterface                                     |
| 返回值 | 无                                                           |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 -其中抛出BPMNError异常时，若该流程为子流程可被父流程CallActivity定义的 边界错误事件捕获。如未定义，业务错误信息包装成结果返回 |

**流程结束**

```java
//创建流程实例
ProcessInstance processInst = SDK.getProcessAPI().createProcessInstance(processDefId, processBusinessKey,uid,createUserDeptId,createUserRoleId,title,vars);
//从默认的开始事件启动流程
List<TaskInstance> tasks = SDK.getProcessAPI().start(processInst, null).fetchActiveTasks();

//自然结束流程实例时（最后一个任务）
SDK.getTaskAPI().completeTask(tasks.get(0), UserContext.fromUID(startUser), true);

//或者终止一个流程实例时
SDK.getProcessAPI().terminate(processInst,UserContext.fromUID(terminateUser))
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.process;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListener;

public class Test_PROCESS_AFTER_COMPLETED extends ExecuteListener {

    public String getDescription() {
        return "测试用例";
    }

    public void execute(ProcessExecutionContext ctx) throws Exception {
        info("流程完成后事件被触发-->" + ctx.getProcessInstance());
    }

}
```

###### [PROCESS_BEFORE_TERMINATE](#PROCESS_BEFORE_TERMINATE)

**流程终止前被触发**

| 项     | 说明                                                         |
| :----- | :----------------------------------------------------------- |
| 抽象类 | InterruptListener                                            |
| 接口   | InterruptListenerInterface                                   |
| 返回值 | 返回false，流程终止被阻止，该分支被孤立中断                  |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 -其中抛出BPMNError异常时，若该流程为子流程可被父流程CallActivity定义的 边界错误事件捕获。如未定义，业务错误信息包装成结果返回 |

**流程终止**

```java
//创建流程实例
ProcessInstance processInst = SDK.getProcessAPI().createProcessInstance(processDefId, processBusinessKey,uid,createUserDeptId,createUserRoleId,title,vars);
//从默认的开始事件启动流程
List<TaskInstance> tasks = SDK.getProcessAPI().start(processInst, null).fetchActiveTasks();

//终止一个流程实例时
SDK.getProcessAPI().terminate(processInst,UserContext.fromUID(terminateUser));
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.process;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.InterruptListener;

public class Test_PROCESS_BEFORE_TERMINATED extends InterruptListener {

    public String getDescription() {
        return "测试用例";
    }

    public boolean execute(ProcessExecutionContext ctx) throws Exception {
        info("流程终止前事件被触发-->" + ctx.getProcessInstance());
        return true;
    }

}
```

###### [PROCESS_AFTER_TERMINATE](#PROCESS_AFTER_TERMINATE)

**流程终止后被触发**

| 项     | 说明                                                         |
| :----- | :----------------------------------------------------------- |
| 抽象类 | ExecuteListener                                              |
| 接口   | ExecuteListenerInterface                                     |
| 返回值 | 无                                                           |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 -其中抛出BPMNError异常时，若该流程为子流程可被父流程CallActivity定义的 边界错误事件捕获。如未定义，业务错误信息包装成结果返回 |

**流程终止**

```java
//创建流程实例
ProcessInstance processInst = SDK.getProcessAPI().createProcessInstance(processDefId, processBusinessKey,uid,createUserDeptId,createUserRoleId,title,vars);
//从默认的开始事件启动流程
List<TaskInstance> tasks = SDK.getProcessAPI().start(processInst, null).fetchActiveTasks();

//终止一个流程实例时
SDK.getProcessAPI().terminate(processInst,UserContext.fromUID(terminateUser));
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.process;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListener;

public class Test_PROCESS_AFTER_TERMINATED extends ExecuteListener {

    public String getDescription() {
        return "测试用例";
    }

    public void execute(ProcessExecutionContext ctx) throws Exception {
        info("流程终止后事件被触发-->" + ctx.getProcessInstance());
    }

}
```

###### [PROCESS_BEFORE_CANCEL](#PROCESS_BEFORE_CANCEL)

**流程取消前被触发**

| 项     | 说明                                                         |
| :----- | :----------------------------------------------------------- |
| 抽象类 | InterruptListener                                            |
| 接口   | InterruptListenerInterface                                   |
| 返回值 | 返回false，流程取消被阻止，该分支被孤立中断                  |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 -其中抛出BPMNError异常时，若该流程为子流程可被父流程CallActivity定义的 边界错误事件捕获。如未定义，业务错误信息包装成结果返回 |

**流程取消**

```java
//创建流程实例
ProcessInstance processInst = SDK.getProcessAPI().createProcessInstance(processDefId, processBusinessKey,uid,createUserDeptId,createUserRoleId,title,vars);
//从默认的开始事件启动流程
List<TaskInstance> tasks = SDK.getProcessAPI().start(processInst, null).fetchActiveTasks();

//取消一个流程实例时
SDK.getProcessAPI().cancel(processInst,UserContext.fromUID(cancelUser))
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.process;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.InterruptListener;
import com.actionsoft.bpms.util.UtilString;

public class Test_PROCESS_BEFORE_CANCEL extends InterruptListener {

    public String getDescription() {
        return "测试用例";
    }

    public boolean execute(ProcessExecutionContext ctx) throws Exception {
        info("流程取消前事件被触发-->" + ctx.getProcessInstance());
        String str1 = (String) ctx.getVariable("str1");
        if (UtilString.isEmpty(str1) || !str1.equals("cancel yes")) {
            info("流程取消前事件-->测试str1必须为cancel yes值才可以取消");
            return false;
        } else {
            info("流程取消前事件-->str1=" + str1);
        }
        return true;
    }

}
```

###### [PROCESS_AFTER_CANCEL](#PROCESS_AFTER_CANCEL)

**流程取消后被触发**

| 项     | 说明                                                         |
| :----- | :----------------------------------------------------------- |
| 抽象类 | ExecuteListener                                              |
| 接口   | ExecuteListenerInterface                                     |
| 返回值 | 无                                                           |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 -其中抛出BPMNError异常时，若该流程为子流程可被父流程CallActivity定义的 边界错误事件捕获。如未定义，业务错误信息包装成结果返回 |

**流程取消**

```java
//创建流程实例
ProcessInstance processInst = SDK.getProcessAPI().createProcessInstance(processDefId, processBusinessKey,uid,createUserDeptId,createUserRoleId,title,vars);
//从默认的开始事件启动流程
List<TaskInstance> tasks = SDK.getProcessAPI().start(processInst, null).fetchActiveTasks();

//取消一个流程实例时
SDK.getProcessAPI().cancel(processInst,UserContext.fromUID(cancelUser));
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.process;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListener;

public class Test_PROCESS_AFTER_CANCEL extends ExecuteListener {

    public String getDescription() {
        return "测试用例";
    }

    public void execute(ProcessExecutionContext ctx) throws Exception {
        info("流程取消后事件被触发-->" + ctx.getProcessInstance());
    }

}
```

###### [PROCESS_BEFORE_DELETE](#PROCESS_BEFORE_DELETE)

**流程删除前被触发**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | InterruptListener                                   |
| 接口   | InterruptListenerInterface                          |
| 返回值 | 返回false，流程删除被阻止                           |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

**流程取消**

```java
//创建流程实例
ProcessInstance processInst = SDK.getProcessAPI().createProcessInstance(processDefId, processBusinessKey,uid,createUserDeptId,createUserRoleId,title,vars);
//从默认的开始事件启动流程
List<TaskInstance> tasks = SDK.getProcessAPI().start(processInst, null).fetchActiveTasks();

//删除一个流程实例时
SDK.getProcessAPI().delete(processInst,UserContext.fromUID(deleteUser));
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.process;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.InterruptListener;
import com.actionsoft.bpms.util.UtilString;

public class Test_PROCESS_BEFORE_DELETED extends InterruptListener {

    public String getDescription() {
        return "测试用例";
    }

    public boolean execute(ProcessExecutionContext ctx) throws Exception {
        info("流程删除前事件被触发-->" + ctx.getProcessInstance());
        String str1 = (String) ctx.getVariable("str1");
        if (UtilString.isEmpty(str1) || !str1.equals("remove yes")) {
            info("流程删除前事件-->测试str1必须为remove yes值才可以删除");
            return false;
        } else {
            info("流程删除前事件-->str1=" + str1);
        }
        return true;
    }

}
```

###### [PROCESS_AFTER_DELETE](#PROCESS_AFTER_DELETE)

**流程删除后被触发**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | ExecuteListener                                     |
| 接口   | ExecuteListenerInterface                            |
| 返回值 | 无                                                  |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

**流程删除**

```java
//创建流程实例
ProcessInstance processInst = SDK.getProcessAPI().createProcessInstance(processDefId, processBusinessKey,uid,createUserDeptId,createUserRoleId,title,vars);
//从默认的开始事件启动流程
List<TaskInstance> tasks = SDK.getProcessAPI().start(processInst, null).fetchActiveTasks();

//删除一个流程实例时
SDK.getProcessAPI().delete(processInst,UserContext.fromUID(deleteUser));
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.process;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListener;

public class Test_PROCESS_AFTER_DELETED extends ExecuteListener {

    public String getDescription() {
        return "测试用例";
    }

    public void execute(ProcessExecutionContext ctx) throws Exception {
        info("流程删除后事件被触发-->" + ctx.getProcessInstance());
    }

}
```

###### [PROCESS_BEFORE_REACTIVATE](#PROCESS_BEFORE_REACTIVATE)

**流程复活前被触发**

将已执行完的流程再次激活使用时触发。

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | InterruptListener                                   |
| 接口   | InterruptListenerInterface                          |
| 返回值 | 返回false，流程复活被阻止                           |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

**流程复活**

```java
//复活一个已结束的流程实例时
SDK.getProcessAPI().reactivate(processInst,targetActivityId,isClearHistory,optUser,targetUser,"原因是要求重新执行");
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.process;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.InterruptListener;
import com.actionsoft.bpms.util.UtilString;

public class Test_PROCESS_BEFORE_REACTIVATE extends InterruptListener {

    public String getDescription() {
        return "测试用例";
    }

    public boolean execute(ProcessExecutionContext ctx) throws Exception {
        info("流程激活前事件被触发-->" + ctx.getProcessInstance());
        return true;
    }

}
```

###### [PROCESS_AFTER_REACTIVATE](#PROCESS_AFTER_REACTIVATE)

**流程复活后被触发**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | ExecuteListener                                     |
| 接口   | ExecuteListenerInterface                            |
| 返回值 | 无                                                  |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

**流程复活**

```java
//复活一个已结束的流程实例时
SDK.getProcessAPI().reactivate(processInst,targetActivityId,isClearHistory,optUser,targetUser,"原因是要求重新执行");
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.process;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListener;

public class Test_PROCESS_AFTER_REACTIVATE extends ExecuteListener {

    public String getDescription() {
        return "测试用例";
    }

    public void execute(ProcessExecutionContext ctx) throws Exception {
        info("流程激活后事件被触发-->" + ctx.getProcessInstance());
    }

}
```

###### [PROCESS_ACTIVITY_ADHOC_BRANCH](#PROCESS_ACTIVITY_ADHOC_BRANCH)

**该流程全局的ADHOC_BRANCH事件，见ACTIVITY_ADHOC_BRANCH说明**

业务场景：**.点击办理按钮校验通过后**

**流程办理**

```java
//创建流程实例
ProcessInstance processInst = SDK.getProcessAPI().createProcessInstance(processDefId, processBusinessKey,uid,createUserDeptId,createUserRoleId,title,vars);
//从默认的开始事件启动流程
List<TaskInstance> tasks = SDK.getProcessAPI().start(processInst, null).fetchActiveTasks();

//完成任务并向下推进时
SDK.getTaskAPI().completeTask(tasks.get(0), UserContext.fromUID(optUser), true);
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.process;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ValueListener;

public class Test_PROCESS_ACTIVITY_ADHOC_BRANCH extends ValueListener {

    public String getDescription() {
        return "测试用例";
    }

    // 规则activityDefId:执行人
    public String execute(ProcessExecutionContext ctx) throws Exception {
        if (ctx.getProcessElement().getName().equals("U1")) {
            info("流程全局跳转事件被触发-->" + ctx.getProcessInstance());
            return "obj_c60c1e1780900001d21d59391c441b54:admin";
        } else {
            return null;
        }
    }

}
```

###### [PROCESS_FORM_BEFORE_LOAD](#PROCESS_FORM_BEFORE_LOAD)

**该流程全局的FORM_BEFORE_LOAD事件**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | ExecuteListener                                     |
| 接口   | ExecuteListenerInterface                            |
| 返回值 | 无                                                  |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

如果该流程某个节点注册了FORM_BEFORE_LOAD事件，则对该节点来说，该PROCESS_FORM_BEFORE_LOAD事件失效。

参见[FORM_BEFORE_LOAD](https://docs.awspaas.com/reference-guide/aws-paas-process-listener-reference-guide/form_event/form_before_load.html)事件

###### [PROCESS_FORM_GRID_FILTER](#PROCESS_FORM_GRID_FILTER)

**该流程全局的FORM_GRID_FILTER事件**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | FormGridFilterListener                              |
| 接口   | ValueListenerInterface                              |
| 返回值 | 无                                                  |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

如果该流程某个节点注册了FORM_GRID_FILTER事件，则对该节点来说，该PROCESS_FORM_GRID_FILTER事件失效。

**场景1：刷新有子表的表单时**

- 过滤子表行记录
- 禁用子表行链接
- 禁止子表行删除
- 设置子表行的样式
- 重新设置子表字段展示数据

**示例**

```java
@Override
public FormGridRowLookAndFeel acceptRowData(ProcessExecutionContext context, List<BOItemModel> boItemList, BO boData) {
    String tableName = context.getParameterOfString(ListenerConst.FORM_EVENT_PARAM_BONAME);
    if (tableName.equals("BO_ACT_PUTONGSUB1")) {
        //创建一个对象
        FormGridRowLookAndFeel diyLookAndFeel = new FormGridRowLookAndFeel();
        String s3 = boData.getString("S3");
        String s4 = boData.getString("S4");
        if (s3 != null && s3.equals("50")) {
            diyLookAndFeel.setLink(false);// 设置这行数据不展示链接
            diyLookAndFeel.setRemove(false);// 设置这行数据不允许删除
            diyLookAndFeel.setCellCSS("style='background-color:yellow;font-color: ffffff;font-weight: bold;height: 125px'");
        }
        if (s3 != null && s3.equals("55")) {
            diyLookAndFeel.setDisplay(false);//不显示这条数据
        }
        if (s4 != null && s4.equals("60")) {
            boData.set("S1", "<img src='../commons/img/add1_16.png' border=0>" + s4);// 重新设定一个字段的值
        }
        //特别说明：
        //如果控制字段子表的`编辑`和`明细`链接的文字的显示隐藏
        //可判断字段名是否是`字段子表`UI组件，然后进行赋值
        //赋值规则：`编辑|明细`
        //如果不显示`编辑`，将该部分留空，如果不显示`明细`，将该部分留空
        //如：`|明细`，`编辑|`，`|`
        boData.set("字段子表字段名", "编辑|明细");

        //处理好之后，将该对象返回
        return diyLookAndFeel;
    }
    return null;//返回null按照原始数据展示子表
}
```

**场景2：指定子表排序**

**示例**

```java
@Override
public String orderByStatement(ProcessExecutionContext context) {
    return "field1 asc,field2 desc";//两种字段组合的排序
    //return "field2 desc";//单个字段排序
}
```

**场景3：自定义子表的表头Html**

**示例**

```java
    /**
 * 构造一个普通子表的表头Html片段，格式：<tr><th></th></tr>，支持多级表头，优先级比表单的自定义表头高
 *
 * @param ctx 流程引擎提供给监听器的上下文对象
 * @param formItemModel 子表项模型，可通过该模型获取到子表的BO模型
 * @param displayPolicy 应用显示策略后的可见的字段列表，其中key为字段名
 * @return
 */
@Override
public String getCustomeTableHeaderHtml(ProcessExecutionContext ctx, FormItemModel formItemModel, List<String> displayPolicy);
    StringBuilder html = new StringBuilder();
    BOModel boModel = BOCache.getInstance().getModel(formItemModel.getBoModelId());//获取BO模型
    List<BOItemModel> boItemList = boModel.getBoItems();
    if (displayPolicy != null && displayPolicy.size() > 0) {//判断子表可见字段的内容
        html.append("<tr>");
        //下面一行是序号列
        html.append("<th align='center' width='50' style='max-width:50px;'><I18N#序号></th>").append("\n");
        //下面四行是复选框列，注意，必须要按照以下格式，否则不能选择数据
        String callback = "callback=\"" + Html.toCallJS("AWSCommonGrid.checkCallback", new Object[] { Html.toJSObj("this"), formItemModel.getId() }) + "\"";
        html.append("<th align='center' width='50' style='max-width:50px;'><input type='checkbox' class='awsui-checkbox check-all' id='check-all-").append(formItemModel.getId()).append("'");
        html.append(" ").append(callback);
        html.append(" group='").append(formItemModel.getId()).append("'/></th>").append("\n");
        for (String fieldName : displayPolicy) {//遍历子表的可见字段，最终形成一个Html片段
            BOItemModel itemModel = BOCache.getInstance().getBOItemModelByBOName(boModel.getName(), fieldName);
            html.append("<th>").append(itemModel.getTitle()).append("</th>");
        }
        html.append("</tr>");
    }
    return html.toString();
}
```

**场景4：自定义AJAX子表的表头JSON**

**示例**

重写**public String getEditGridHeaderJSON(ProcessExecutionContext ctx, FormItemModel formItemModel, List displayPolicy)**方法

```java
   /**
 * 构造一个AJAX子表的表头JSON，格式：
 ['表字段', {
    title: "表头",
    width: 250,
    align: 'center',
    colModel: [{
        title: "二级栏目1",
        align: 'center',
        colModel: [
            表字段1,
            表字段2,
            表字段3
        ]
    }, {
        title: "二级栏目2",
        align: 'center',
        colModel: [
            表字段1,
            表字段2
        ]
    }]
}]，
 *
 *
 * @param ctx 流程引擎提供给监听器的上下文对象
 * @param formItemModel 子表项模型，可通过该模型获取到子表的BO模型
 * @param displayPolicy 应用显示策略后的可见的字段列表，其中key为字段名
 * @return
 */
@Override
public String getEditGridHeaderJSON(ProcessExecutionContext ctx, FormItemModel formItemModel, List<String> displayPolicy);
    StringBuilder headJson = new StringBuilder();
    BOModel boModel = BOCache.getInstance().getModel(formItemModel.getBoModelId());//获取BO模型
    List<BOItemModel> boItemList = boModel.getBoItems();
    if (displayPolicy != null && displayPolicy.size() > 0) {//判断子表可见字段的内容
    /*    String headJson = "['ASSESSOBJECT','WEIGHT',
                + "{'title':'第一季度','align':'center','colModel':['CONSTANT11','CONSTANT12','CONSTANT13','CONSTANT14','CONSTANT15']},"+
                  "{'title':'第二季度','align':'center','colModel':['CONSTANT21','CONSTANT22','CONSTANT23']},"+
                  "{'title':'第三季度','align':'center','colModel':['CONSTANT31','CONSTANT32','CONSTANT33']},"+
                  "{'title':'第四季度','align':'center','colModel':['CONSTANT41','CONSTANT42','CONSTANT43']}+
                   ]";*/
        headJson.append("[");
        String season1 = "{'title':'第一季度','align':'center','colModel':[";//表头 title 必填 ，align ，width 选填
        String season2 = "{'title':'第二季度','align':'center','colModel':[";//colModel是字段直接写字段名称
        String season3 = "{'title':'第三季度','align':'center','colModel':[";
        String season4 = "{'title':'第四季度','align':'center','colModel':[";
        for (String fieldName : displayPolicy) {//遍历子表的可见字段，最终形成一个JSON片段
            BOItemModel itemModel = BOCache.getInstance().getBOItemModelByBOName(boModel.getName(), fieldName);
            if(itemModel.getName().equals("ASSESSOBJECT")){
               headJson.append("'").append(itemModel.getName()).append("',");//没有多级表头直接展示字段
            }else if(itemModel.getName().contains("WEIGHT")){
                 headJson.append("'").append(itemModel.getName()).append("',");
            }else if(itemModel.getName().contains("CONSTANT1")){
                season1+="'"+itemModel.getName()+"'"+",";
            }else if(itemModel.getName().contains("CONSTANT2")){
                season2+="'"+itemModel.getName()+"'"+",";
            }else if(itemModel.getName().contains("CONSTANT3")){
                season3+="'"+itemModel.getName()+"'"+",";
            }else if(itemModel.getName().contains("CONSTANT4")){
                season4+="'"+itemModel.getName()+"'"+",";
            }
        }
        season1 = season1.substring(0,season1.length()-1)+"]},";
        season2 = season2.substring(0,season2.length()-1)+"]},";
        season3 = season3.substring(0,season3.length()-1)+"]},";
        season4 = season4.substring(0,season4.length()-1)+"]}";
        headJson.append(season1).append(season2).append(season3).append(season4).append("]");
    }
    return headJson.toString();
}
```

###### [PROCESS_BEFORE_RESTART](#PROCESS_BEFORE_RESTART)

**流程重置前被触发**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | InterruptListener                                   |
| 接口   | InterruptListenerInterface                          |
| 返回值 | 返回false，流程重置被阻止                           |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

**流程重置**

```java
//重置流程实例
 SDK.getProcessAPI().restart( processInst,  restartReason);
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.process;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.InterruptListener;
import com.actionsoft.bpms.util.UtilString;

public class Test_PROCESS_BEFORE_DELETED extends InterruptListener {

    public String getDescription() {
        return "测试用例";
    }

    public boolean execute(ProcessExecutionContext ctx) throws Exception {
        info("流程重置前事件被触发-->" + ctx.getProcessInstance());
        return true;
    }

}
```

###### [PROCESS_AFTER_RESTART](#PROCESS_AFTER_RESTART)

**流程重置后被触发**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | ExecuteListener                                     |
| 接口   | ExecuteListenerInterface                            |
| 返回值 | 无                                                  |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

**流程重置**

```java
//重置流程实例
 SDK.getProcessAPI().restart( processInst,  restartReason);
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.process;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListener;

public class Test_PROCESS_AFTER_CANCEL extends ExecuteListener {

    public String getDescription() {
        return "测试用例";
    }

    public void execute(ProcessExecutionContext ctx) throws Exception {
        info("流程重置后事件被触发-->" + ctx.getProcessInstance());
    }

}
```

##### 6、节点通用事件

###### [TASK_BEFORE_COMPLETE](#TASK_BEFORE_COMPLETE)

**任务完成（结束）前被触发**

| 项     | 说明                                                         |
| :----- | :----------------------------------------------------------- |
| 抽象类 | InterruptListener                                            |
| 接口   | InterruptListenerInterface                                   |
| 返回值 | 返回false，任务完成被阻止                                    |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 -其中抛出BPMNError异常时，该节点定义的错误边界事件将被捕获， 若未捕获且该流程为子流程，可被父流程CallActivity定义的 边界错误事件捕获。如未定义，业务错误信息包装成结果返回 |

**完成任务往下推**

```java
//完成任务并向下推进时
SDK.getTaskAPI().completeTask(taskInst, UserContext.fromUID(optUser), true);
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.usertask;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.InterruptListener;
import com.actionsoft.bpms.bpmn.engine.listener.InterruptListenerInterface;

public class Test_TASK_BEFORE_COMPLETE extends InterruptListener implements InterruptListenerInterface {

    public boolean execute(ProcessExecutionContext ctx) throws Exception {
        info("任务结束前可以阻止被触发-->" + ctx.getTaskInstance());
        // throw new BPMNError("UCode01", "User biz error");
        return true;
    }

}
```

###### [TASK_AFTER_COMPLETE](#TASK_AFTER_COMPLETE)

**任务完成（结束）后被触发**

| 项     | 说明                                                         |
| :----- | :----------------------------------------------------------- |
| 抽象类 | ExecuteListener                                              |
| 接口   | ExecuteListenerInterface                                     |
| 返回值 | 无                                                           |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 -其中抛出BPMNError异常时，该节点定义的错误边界事件将被捕获， 若未捕获且该流程为子流程，可被父流程CallActivity定义的 边界错误事件捕获。如未定义，业务错误信息包装成结果返回 |

**完成任务并向下推进时**

```java
//完成任务并向下推进时
SDK.getTaskAPI().completeTask(taskInst, UserContext.fromUID(optUser), true);
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.usertask;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListener;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListenerInterface;

public class Test_TASK_AFTER_COMPLETE extends ExecuteListener implements ExecuteListenerInterface {

    public void execute(ProcessExecutionContext ctx) throws Exception {
        info("任务结束后可以补偿被触发-->" + ctx.getTaskInstance());
        // throw new BPMNError("UCode02", "User biz error");
    }

}
```

###### [TASK_SUSPEND](#TASK_SUSPEND)

**任务挂起后被触发**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | ExecuteListener                                     |
| 接口   | ExecuteListenerInterface                            |
| 返回值 | 无                                                  |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

**挂起任务**

```java
//挂起任务
SDK.getTaskAPI().suspend(taskInst);
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.usertask;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListener;

public class Test_TASK_SUSPEND extends ExecuteListener {

    public String getDescription() {
        return "测试用例";
    }

    public void execute(ProcessExecutionContext ctx) throws Exception {
        info("任务挂起后事件被触发-->" + ctx.getTaskInstance());
    }

}
```

###### [TASK_RESUME](#TASK_RESUME)

**任务恢复后被触发**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | ExecuteListener                                     |
| 接口   | ExecuteListenerInterface                            |
| 返回值 | 无                                                  |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

**恢复任务**

```java
//恢复任务
SDK.getTaskAPI().resume(taskInst);
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.usertask;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListener;

public class Test_TASK_RESUME extends ExecuteListener {

    public String getDescription() {
        return "测试用例";
    }

    public void execute(ProcessExecutionContext ctx) throws Exception {
        info("任务恢复后事件被触发-->" + ctx.getTaskInstance());
    }

}
```

###### [ACTIVITY_BEFORE_LEAVE](#ACTIVITY_BEFORE_LEAVE)

**节点离开前被触发**

| 项     | 说明                                                         |
| :----- | :----------------------------------------------------------- |
| 抽象类 | InterruptListener                                            |
| 接口   | InterruptListenerInterface                                   |
| 返回值 | 返回false，节点离开被阻止，该分支被中断于此                  |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 -其中抛出BPMNError异常时，该节点定义的错误边界事件将被捕获， 若未捕获且该流程为子流程，可被父流程CallActivity定义的 边界错误事件捕获。如未定义，业务错误信息包装成结果返回 |

**完成任务并向下推进**

```java
//该节点所有任务都将结束，完成任务并向下推进时
SDK.getTaskAPI().completeTask(taskInst, UserContext.fromUID(optUser), true);
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.usertask;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.InterruptListener;
import com.actionsoft.bpms.bpmn.engine.listener.InterruptListenerInterface;

public class Test_ACTIVITY_BEFORE_LEAVE extends InterruptListener implements InterruptListenerInterface {

    public boolean execute(ProcessExecutionContext ctx) throws Exception {
        info("节点离开前可以阻止被触发-->" + ctx.getTaskInstance());
        // throw new BPMNError("UCode03", "User biz error");
        return true;
    }

}
```

###### [ACTIVITY_AFTER_LEAVE](#ACTIVITY_AFTER_LEAVE)

**节点离开后被触发**

| 项     | 说明                                                         |
| :----- | :----------------------------------------------------------- |
| 抽象类 | ExecuteListener                                              |
| 接口   | ExecuteListenerInterface                                     |
| 返回值 | 无                                                           |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 -其中抛出BPMNError异常时，该节点定义的错误边界事件将被捕获， 若未捕获且该流程为子流程，可被父流程CallActivity定义的 边界错误事件捕获。如未定义，业务错误信息包装成结果返回 |

**完成任务并向下推进**

```java
//该节点所有任务都将结束，完成任务并向下推进时
SDK.getTaskAPI().completeTask(taskInst, UserContext.fromUID(optUser), true);
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.usertask;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListener;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListenerInterface;

public class Test_ACTIVITY_AFTER_LEAVE extends ExecuteListener implements ExecuteListenerInterface {

    public void execute(ProcessExecutionContext ctx) throws Exception {
        info("节点结束后可以补偿被触发-->" + ctx.getTaskInstance());
        // throw new BPMNError("UCode04", "User biz error");
    }

}
```

##### 7、人工任务专有事件

###### [ACTIVITY_ADHOC_BRANCH](#ACTIVITY_ADHOC_BRANCH)

**程序指定后继路线和参与者，上一节点准备离开时触发**

| 项     | 说明                                                         |
| :----- | :----------------------------------------------------------- |
| 抽象类 | ValueListener                                                |
| 接口   | ValueListenerInterface                                       |
| 返回值 | -一个格式化的字符串值，如果返回null不干涉 - 格式：节点定义Id:参与者账户 -如果指定节点定义Id，后继路线跳转到该节点 -如果指定参与者账户（多个空格隔开），后继节点的参与者以该值为准 |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 -其中抛出BPMNError异常时，该节点定义的错误边界事件将被捕获， 若未捕获且该流程为子流程，可被父流程CallActivity定义的 边界错误事件捕获。如未定义，业务错误信息包装成结果返回 |

**完成任务并向下推进**

```java
//完成任务并向下推进时
SDK.getTaskAPI().completeTask(tasks.get(0), UserContext.fromUID(optUser), true);
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.usertask;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ValueListener;
import com.actionsoft.bpms.bpmn.engine.listener.ValueListenerInterface;

public class Test_ACTIVITY_ADHOC_BRANCH extends ValueListener implements ValueListenerInterface {

    public String execute(ProcessExecutionContext ctx) throws Exception {
        info("程序指定后继路线和参与者-->" + ctx.getTaskInstance());
        if (ctx.getProcessElement().getName().equals("U1")) {
            return "obj_c649377f56b000012274a6807f401783:admin";
        } else {
            return null;
        }
    }

}
```

###### [ACTIVITY_CONFIRM_PARTICIPANTS](#ACTIVITY_CONFIRM_PARTICIPANTS)

**节点就绪参与者即将产生任务**

| 项     | 说明                                                         |
| :----- | :----------------------------------------------------------- |
| 抽象类 | ValueListener                                                |
| 接口   | ValueListenerInterface                                       |
| 返回值 | - 一个格式化的字符串值，如果返回null不干涉 - 格式：参与者账户，开发者可以在ProcessExecutionContext上下文中调用 getParameterOfString("$PARTICIPANTS")获得引擎通过路由或ACTIVITY_ADHOC_BRANCH事件获取的参与者账户（多个空格隔开） -如果多个账户，将按该节点的多例模式进行处理 |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 -其中抛出BPMNError异常时，该节点定义的错误边界事件将被捕获， 若未捕获且该流程为子流程，可被父流程CallActivity定义的 边界错误事件捕获。如未定义，业务错误信息包装成结果返回 |

业务场景：**获得下个执行任务路径时**

**完成任务并向下推进时**

```java
//完成任务并向下推进时
SDK.getTaskAPI().completeTask(tasks.get(0), UserContext.fromUID(optUser), true);
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.usertask;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ValueListener;
import com.actionsoft.bpms.bpmn.engine.listener.ValueListenerInterface;

public class Test_ACTIVITY_CONFIRM_PARTICIPANTS extends ValueListener implements ValueListenerInterface {

    public String execute(ProcessExecutionContext ctx) throws Exception {
        info("节点就绪参与者即将产生任务被触发");
        String participants = ctx.getParameterOfString("$PARTICIPANTS");
        info("即将给[" + participants + "]创建人工任务");
        return "admin admin admin";// 不干预当前的委派过程
    }
}
```

###### [TASK_BEFORE_UNDO](#TASK_BEFORE_UNDO)

**任务收回前被触发**

| 项     | 说明                                |
| :----- | :---------------------------------- |
| 抽象类 | InterruptListener                   |
| 接口   | InterruptListenerInterface          |
| 返回值 | 返回false，任务收回被阻止           |
| 异常   | -如抛出异常时，异常被包装成结果返回 |

**收回任务**

```java
//收回任务
SDK.getTaskAPI().undoTask(taskInstId, uid, undoReason);
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.usertask;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.InterruptListener;
import com.actionsoft.bpms.bpmn.engine.listener.InterruptListenerInterface;

public class Test_TASK_BEFORE_UNDO extends InterruptListener implements InterruptListenerInterface {

    public boolean execute(ProcessExecutionContext ctx) throws Exception {
        info("任务收回前可以阻止被触发-->" + ctx.getTaskInstance());
        // throw new BPMNError("UCode01", "User biz error");
        return true;
    }

}
```

###### [TASK_AFTER_UNDO](#TASK_AFTER_UNDO)

**任务收回后被触发**

| 项     | 说明                                |
| :----- | :---------------------------------- |
| 抽象类 | ExecuteListener                     |
| 接口   | ExecuteListenerInterface            |
| 返回值 | 无                                  |
| 异常   | -如抛出异常时，异常被包装成结果返回 |

**收回任务**

```java
package com.actionsoft.apps.poc.api.local.process.listener.usertask;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListener;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListenerInterface;

public class Test_TASK_AFTER_UNDO extends ExecuteListener implements ExecuteListenerInterface {

    public void execute(ProcessExecutionContext ctx) throws Exception {
        info("任务收回后可以补偿被触发-->" + ctx.getTaskInstance());
        // throw new BPMNError("UCode02", "User biz error");
    }

}
```

###### [TASK_AFTER_CREATED](#TASK_AFTER_CREATED)

**任务创建后被触发**

| 项     | 说明                                |
| :----- | :---------------------------------- |
| 抽象类 | ExecuteListener                     |
| 接口   | ExecuteListenerInterface            |
| 返回值 | 无                                  |
| 异常   | -如抛出异常时，异常被包装成结果返回 |

**创建任务**

```java
// 创建任务
SDK.getTaskAPI().createUserTaskInstance(processInst, parentTaskInstModel, userContext,targetActivityDefId,participant,title);
```

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.usertask;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListener;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListenerInterface;

public class Test_TASK_AFTER_CREATED extends ExecuteListener implements ExecuteListenerInterface {

    public void execute(ProcessExecutionContext ctx) throws Exception {
        info("任务创建后可以补偿被触发-->" + ctx.getTaskInstance());
        // throw new BPMNError("UCode02", "User biz error");
    }

}
```

##### 8、子流程任务专有事件

###### [CALLACTIVITY_BEFORE_SUBPROCESS_START](#CALLACTIVITY_BEFORE_SUBPROCESS_START)

**子流程实例创建后启动前触发**

| 项     | 说明                                                         |
| :----- | :----------------------------------------------------------- |
| 抽象类 | ExecuteListener                                              |
| 接口   | ExecuteListenerInterface                                     |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 -其中抛出BPMNError异常时，该节点定义的错误边界事件将被捕获， 若未捕获且该流程为子流程，可被父流程CallActivity定义的 边界错误事件捕获。如未定义，业务错误信息包装成结果返回 |
| 参数   | CallActivityDefinitionConst.PARAM_CALLACTIVITY_CONTEXT       |

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.callactivity;

import com.actionsoft.bpms.bpmn.constant.CallActivityDefinitionConst;
import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.core.delegate.TaskBehaviorContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListener;

public class Test_CALLACTIVITY_BEFORE_SUBPROCESS_START extends ExecuteListener {

    @Override
    public void execute(ProcessExecutionContext ctx) throws Exception {
        // 父流程实例对象
        System.out.println("Parent ProcessInstance=" + ctx.getProcessInstance());
        // 子流程实例上下文，此阶段子流程实例已创建，未开始流程（无任务实例）
        TaskBehaviorContext subProcessCtx = (TaskBehaviorContext) ctx.getParameter(CallActivityDefinitionConst.PARAM_CALLACTIVITY_CONTEXT);
        // 子流程实例
        System.out.println("Sub ProcessInstance=" + subProcessCtx.getProcessInstance());
    }

}
```

###### [CALLACTIVITY_AFTER_SUBPROCESS_COMPLETE](#CALLACTIVITY_AFTER_SUBPROCESS_COMPLETE)

**子流程实例结束后触发**

| 项     | 说明                                                         |
| :----- | :----------------------------------------------------------- |
| 抽象类 | ExecuteListener                                              |
| 接口   | ExecuteListenerInterface                                     |
| 异常   | 抛出异常时，如果该子流程实例是单例或多例的最后一个子流程实例，流程将中断在父流程的`子流程任务` |
| 参数   | CallActivityDefinitionConst.PARAM_CALLACTIVITY_CONTEXT       |

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.callactivity;

import com.actionsoft.bpms.bpmn.constant.CallActivityDefinitionConst;
import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.core.delegate.TaskBehaviorContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListener;

public class Test_CALLACTIVITY_AFTER_SUBPROCESS_COMPLETE extends ExecuteListener {

    @Override
    public void execute(ProcessExecutionContext ctx) throws Exception {
        // 父流程实例对象
        System.out.println("Parent ProcessInstance=" + ctx.getProcessInstance());
        // 子流程实例上下文
        TaskBehaviorContext subProcessCtx = (TaskBehaviorContext) ctx.getParameter(CallActivityDefinitionConst.PARAM_CALLACTIVITY_CONTEXT);
        // 子流程实例
        System.out.println("Sub ProcessInstance=" + subProcessCtx.getProcessInstance());
    }

}
```

##### 9、流程表单事件

###### [FORM_COMPLETE_VALIDATE](#FORM_COMPLETE_VALIDATE)

**流程表单办理前校验,流程办理前被触发**

| 项     | 说明                                                         |
| :----- | :----------------------------------------------------------- |
| 抽象类 | InterruptListener                                            |
| 接口   | InterruptListenerInterface                                   |
| 返回值 | 返回false，流程办理被阻止                                    |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 -其中抛出BPMNError异常时，该节点定义的错误边界事件将被捕获， 若未捕获且该流程为子流程，可被父流程CallActivity定义的 边界错误事件捕获。如未定义，业务错误信息包装成结果返回 |

**示例**

```java
package com.actionsoft.apps.poc.api.local.process.listener.usertask;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.InterruptListener;
import com.actionsoft.bpms.bpmn.engine.listener.InterruptListenerInterface;

public class Test_FORM_COMPLETE_VALIDATE extends InterruptListener implements InterruptListenerInterface {

    public boolean execute(ProcessExecutionContext ctx) throws Exception {
        //参数获取
        //注意：除特殊说明外，下列参数仅在该事件中场景有效
        //记录ID
        String boId = ctx.getParameterOfString(ListenerConst.FORM_EVENT_PARAM_BOID);
        //表单ID
        String formId = ctx.getParameterOfString(ListenerConst.FORM_EVENT_PARAM_FORMID);
        //BO表名
        String boName = ctx.getParameterOfString(ListenerConst.FORM_EVENT_PARAM_BONAME);

        //业务异常代码（自定义）
        //业务异常信息（自定义）
        throw new BPMNError("0312","订单尚未审核，不能进行支付操作");
    }

}
```

###### [FORM_TOOLBAR_BUILD](#FORM_TOOLBAR_BUILD)

**表单工具栏构建**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | FormToolbarBuilderListener                          |
| 接口   | ValueListenerInterface                              |
| 返回值 | List<ButtonModel>                                   |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

**按钮执行动作**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | ValueListener                                       |
| 接口   | ValueListenerInterface                              |
| 返回值 | String                                              |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

###### [FORM_BEFORE_LOAD](#FORM_BEFORE_LOAD)

**表单构建前被触发**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | ExecuteListener                                     |
| 接口   | ExecuteListenerInterface                            |
| 返回值 | 无                                                  |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

###### [FORM_AFTER_LOAD](#FORM_AFTER_LOAD)

**表单构建后被触发**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | ExecuteListener                                     |
| 接口   | ExecuteListenerInterface                            |
| 返回值 | 无                                                  |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

###### [FORM_BEFORE_SAVE](#FORM_BEFORE_SAVE)

**表单(或子表)保存前被触发**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | InterruptListener                                   |
| 接口   | InterruptListenerInterface                          |
| 返回值 | 返回false，表单保存被阻止                           |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

通常，该事件用于在保存前的最后一次校验，可以用来阻止保存

**示例**

```java
package com.actionsoft.apps.poc.form.event;

import com.actionsoft.bpms.bo.engine.BO;
import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.InterruptListener;
import com.actionsoft.sdk.local.SDK;

public class TestFormBeforeSave extends InterruptListener {

    public String getDescription() {
        return "表单保存前的事件测试";
    }

    public String getProvider() {
        return "Actionsoft";
    }

    public String getVersion() {
        return "1.0";
    }

    public boolean execute(ProcessExecutionContext param) throws Exception {
        //参数获取
        //注意：除特殊说明外，下列参数仅在该事件中场景有效
        //记录ID
        String boId = param.getParameterOfString(ListenerConst.FORM_EVENT_PARAM_BOID);
        //表单ID
        String formId = param.getParameterOfString(ListenerConst.FORM_EVENT_PARAM_FORMID);
        //BO表名
        String boName = param.getParameterOfString(ListenerConst.FORM_EVENT_PARAM_BONAME);

        // 保存前的表单数据，注意：该参数针对不同场景获取内容会有所不同
        // 主表中的保存场景获取主表数据；普通子表页面的保存场景获取的是该条子表的数据；如果需要获得其他数据请使用BOQueryAPI获取
        BO formData = (BO) param.getParameter(ListenerConst.FORM_EVENT_PARAM_FORMDATA);

        // 获取Ajax子表的数据，由于Ajax子表的数据会同主表保存动作一起触发，需要使用该参数获取
        // 在Ajax子表的工具栏上的“保存”动作和主表的“保存”动作中有效
        // 注意：该数据并不是从数据库中获取，获取的数据取决于表单上对Ajax子表新增的数据与修改的数据的和
        List<BO> gridData = (List) param.getParameter(ListenerConst.FORM_EVENT_PARAM_GRIDDATA);
        //遍历子表的数据
        for (BO rowData : gridData) {
            //下面一行示例代码，可以获取Ajax子表的每行记录的新建状态
            boolean rowDataIsCreate = Boolean.parseBoolean(rowData.getString("isCreate"));//注意：isCreate并不是BO的一个字段，该字段是有接口上层赋值的
        }

        // 该记录是否新建的状态，由于机制调整，BO对象中的ID是不为空的，不能通过ID判断记录是否处于新建状态还是修改状态
        //注意：该参数仅适用保存前（后）事件中，该参数仅能获取主表的是否新建状态
        boolean isCreate = param.getParameterOfBoolean(ListenerConst.FORM_EVENT_PARAM_ISCREATE);

        //该记录是否通过复制功能创建，用于普通子表的复制时判断该状态
        //注意：该参数仅适用保存前（后）事件中
        boolean isCopy = param.getParameterOfBoolean(ListenerConst.FORM_EVENT_PARAM_ISCOPY);

        return true;//返回true可以正常保存表单
        //return false;//可阻止表单保存
        //或者直接抛出BPMNErr异常
        //throw new BPMNError("0313","数据不完整，不能进行保存操作");
    }
}
```

###### [FORM_AFTER_SAVE](#FORM_AFTER_SAVE)

**表单(或子表)保存后被触发**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | ExecuteListener                                     |
| 接口   | ExecuteListenerInterface                            |
| 返回值 | 无                                                  |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

通常，该事件用于补偿处理一些业务逻辑

**示例**

```java
package com.actionsoft.apps.poc.form.event;

import com.actionsoft.bpms.bo.engine.BO;
import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListener;
import com.actionsoft.sdk.local.SDK;

public class TestFormAfterSave extends ExecuteListener {

    public String getDescription() {
        return "表单保存后的事件测试";
    }

    public String getProvider() {
        return "Actionsoft";
    }

    public String getVersion() {
        return "1.0";
    }

    public void execute(ProcessExecutionContext param) throws Exception {
        //参数获取
        //注意：除特殊说明外，下列参数仅在该事件中场景有效
        //记录ID
        String boId = param.getParameterOfString(ListenerConst.FORM_EVENT_PARAM_BOID);
        //表单ID
        String formId = param.getParameterOfString(ListenerConst.FORM_EVENT_PARAM_FORMID);
        //BO表名
        String boName = param.getParameterOfString(ListenerConst.FORM_EVENT_PARAM_BONAME);
        // 保存前的表单数据，注意：该参数针对不同场景获取内容会有所不同
        // 主表场景获取主表数据；普通子表页面的场景获取的是该条子表的数据；获取其他的数据请使用BOQueryAPI获取
        // 注意：这个数据是在保存前放入的，经过保存之后，这些数据和数据库中的数据是一致的
        BO formData = (BO) param.getParameter(ListenerConst.FORM_EVENT_PARAM_FORMDATA);

        // 获取Ajax子表在保存时的数据，由于Ajax子表的数据会同主表保存动作一起触发，需要使用该参数获取
        // 在Ajax子表的工具栏上的“保存”动作和主表的“保存”动作中有效
        // 注意：这个数据是在保存前放入的，经过保存之后，这些数据和数据库中的数据是一致的
        List<BO> gridData = (List) param.getParameter(ListenerConst.FORM_EVENT_PARAM_GRIDDATA);
        // 该记录是否新建的状态，由于机制调整，BO对象中的ID是不为空的，不能通过ID判断记录是否处于新建状态还是修改状态
        //注意：该参数仅适用保存前（后）事件中
        boolean isCreate = param.getParameterOfBoolean(ListenerConst.FORM_EVENT_PARAM_ISCREATE);
        //该记录是否通过复制功能创建，用于普通子表的复制时判断该状态
        //注意：该参数仅适用保存前（后）事件中
        boolean isCopy = param.getParameterOfBoolean(ListenerConst.FORM_EVENT_PARAM_ISCOPY);

        //注意
        //当该事件处于子表导入后被触发的场景时仅能获取到“ListenerConst.FORM_EVENT_PARAM_BONAME”和“ListenerConst.FORM_EVENT_PARAM_FORMID”
        //参数“ListenerConst.FORM_EVENT_PARAM_ISCREATE”E和“ListenerConst.FORM_EVENT_PARAM_ISCOPY”在该场景不适用

        //...
    }
}
```

###### [FORM_BEFORE_REMOVE](#FORM_BEFORE_REMOVE)

**表单子表记录删除前被触发**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | InterruptListener                                   |
| 接口   | InterruptListenerInterface                          |
| 返回值 | 返回false，子表删除数据动作被阻止                   |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

**示例**

```java
package com.actionsoft.apps.poc.form.event;

import com.actionsoft.bpms.bo.engine.BO;
import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.InterruptListener;
import com.actionsoft.sdk.local.SDK;

public class TestFormBeforeRemove extends InterruptListener {

    public String getDescription() {
        return "表单子表记录删除前的事件测试";
    }

    public String getProvider() {
        return "Actionsoft";
    }

    public String getVersion() {
        return "1.0";
    }

    public boolean execute(ProcessExecutionContext param) throws Exception {
        //参数获取
        //注意：除特殊说明外，下列参数仅在该事件中场景有效
        //子表单项ID
        String formItemId = param.getParameterOfString(ListenerConst.FORM_EVENT_PARAM_FORMID);
        //BO表名
        String boName = param.getParameterOfString(ListenerConst.FORM_EVENT_PARAM_BONAME);

        //即将被删除的记录，可用与校验该数据是否存在依赖关系，是否可被删除等
        BO bo = (BO) param.getParameter(ListenerConst.FORM_EVENT_PARAM_REMOVED_BO);

        //注意：由于使用了事务，操作数据库需要使用如下方式获取的Connection连接
        //该参数仅在表单子表记录删除前（后）有效
        Connection conn = (Connection) param.getParameter(ListenerConst.FORM_EVENT_PARAM_CONNECTION);

        return true;//返回true可以删除记录
        //return false;//可阻止记录删除
        //或者直接抛出BPMNErr异常
        //throw new BPMNError("0314","数据被使用，不能进行删除操作");
    }
}
```

###### [FORM_AFTER_REMOVE](#FORM_AFTER_REMOVE)

**表单子表记录删除后被触发**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | ExecuteListener                                     |
| 接口   | ExecuteListenerInterface                            |
| 返回值 | 无                                                  |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

**示例**

```java
package com.actionsoft.apps.poc.form.event;

import com.actionsoft.bpms.bo.engine.BO;
import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExecuteListener;
import com.actionsoft.sdk.local.SDK;

public class TestFormAfterRemove extends ExecuteListener {

    public String getDescription() {
        return "表单子表记录删除后的事件测试";
    }

    public String getProvider() {
        return "Actionsoft";
    }

    public String getVersion() {
        return "1.0";
    }

    public void execute(ProcessExecutionContext param) throws Exception {
        //参数获取
        //注意：除特殊说明外，下列参数仅在该事件中场景有效
        //子表单项ID
        String formItemId = param.getParameterOfString(ListenerConst.FORM_EVENT_PARAM_FORMID);
        //BO表名
        String boName = param.getParameterOfString(ListenerConst.FORM_EVENT_PARAM_BONAME);

        //被删除的记录，可读取该信息，进行删除后的补偿操作
        BO bo = (BO) param.getParameter(ListenerConst.FORM_EVENT_PARAM_REMOVED_BO);

        //注意：由于使用了事务，操作数据库需要使用如下方式获取的Connection连接
        //该参数仅在表单子表记录删除前（后）有效
        Connection conn = (Connection) param.getParameter(ListenerConst.FORM_EVENT_PARAM_CONNECTION);

        //执行删除后的业务补偿
    }
}
```

###### [FORM_GRID_FILTER](#FORM_GRID_FILTER)

**表单子表过滤(支持普通子表、Ajax子表)**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | FormGridFilterListener                              |
| 接口   | ValueListenerInterface                              |
| 返回值 | 无                                                  |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

###### [FORM_GRID_EXCEL_TRANSFORM](#FORM_GRID_EXCEL_TRANSFORM)

**表单子表Excle转换处理**

| 项     | 说明                                                |
| :----- | :-------------------------------------------------- |
| 抽象类 | ExcelTransformListener                              |
| 返回值 | 无                                                  |
| 异常   | -如抛出异常时，异常被包装成结果返回，后继执行被中断 |

**示例**

```java
package com.actionsoft.apps.poc.form.event;

import org.apache.poi.hssf.usermodel.HSSFWorkbook;
import org.apache.poi.ss.usermodel.Workbook;
import org.apache.poi.xssf.streaming.SXSSFWorkbook;

import com.actionsoft.bpms.bpmn.engine.core.delegate.ProcessExecutionContext;
import com.actionsoft.bpms.bpmn.engine.listener.ExcelTransformListener;
import com.actionsoft.bpms.bpmn.engine.listener.ListenerConst;

public class TestFormExcelTransform extends ExcelTransformListener {

    public String getDescription() {
        return "表单中，下载、上传Excel后处理Excel文件的事件测试";
    }

    public String getProvider() {
        return "Actionsoft";
    }

    public String getVersion() {
        return "1.0";
    }

    @Override
    public Workbook fixExcel(ProcessExecutionContext ctx, Workbook wb) {
        //参数获取
        //注意：除特殊说明外，下列参数仅在该事件中场景有效
        ctx.getUserContext();// 用户上下文对象
        // 时间点的常量见上表
        String timeState = ctx.getParameterOfString(ListenerConst.FORM_EVENT_PARAM_EXCEL_TIMESTATE);// 通过该值判断当前事件所处的时间点
        // 判断方式
        if (ListenerConst.FORM_EXCEL_TIMESTATE_IMPORT_BEFORE.equals(timeState)) {
            // ...
        }

        //wb对象可以构造为HSSFWorkbook或者SXSSFWorkbook
        if (wb instanceof HSSFWorkbook) {
            // 解析Excel（xls格式）
        }
        if (wb instanceof SXSSFWorkbook) {
            // 解析Excel（xlsx格式）
        }

        // 如果想要阻止下载或者上传的后续操作，可以return null;

        return wb;//注意，即使对该对象进行修改，上层程序也不会读取新的数据。
    }

}
```

##### 10、输出业务对话

###### 抛出BPMNError

```java
//业务异常代码（自定义）
//业务异常信息（自定义）
throw new BPMNError("ERR0312","订单尚未审核，不能进行支付操作");
```

###### 使用AlertMessage

**仅适用以下事件**

- FORM_COMPLETE_VALIDATE
- FORM_AFTER_SAVE
- FORM_BEFORE_LOAD

```java
//以下是表单横幅消息的示例代码，多条消息顺序按照添加的顺序展示
//第一个参数是BO名称，第二个参数是消息内容，这是一个默认的消息
ctx.addAlertMessage(boName, "测试横幅警告消息");
ctx.addAlertMessage(boName, "第二个消息");
ctx.addAlertMessageInfo(boName, "我是第三个提醒消息");//这是一个普通的消息
ctx.addAlertMessageWarn(boName, "我是第四个警告消息");//这是一个警告的消息
ctx.addAlertMessageError(boName, "我是第五个错误消息");//这是一个错误的消息
//另外提供了自定义外观的消息，第三个参数指定背景色，第四个参数指定文字颜色
ctx.addAlertMessage(boName, "消息", "bgColor", "fontColor");
```
