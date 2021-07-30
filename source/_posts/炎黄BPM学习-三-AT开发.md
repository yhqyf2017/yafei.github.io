---
title: 炎黄BPM学习(三)-AT开发
date: 2021-06-17 10:55:27
tags:
- 炎黄BPM
- BPM
categories: 
- BPM
cover: false
---



### 应用场景

@公式(又称为@命令/AT公式)是一个预先定义，服务器端解析执行的函数公式（例如获得当前服务器日期，可使用`@date`）。

### 开发步骤

1. 继承`AbstExpression`抽象类，实现公式的处理逻辑
2. 用`AtFormulaPluginProfile`描述这个插件，注册到该应用的`PluginListener`类。
3. 场景模拟，调试

### 注册语法

```java
//注册AT公式
list.add(new AtFormulaPluginProfile(groupName, syntax,
clazz, title, desc));
```

- `groupName`-分类，首先检查当前AT公式库的分类是否符合你的公式，如果不符合可以给定一个合理的新分类
- `syntax`-语法，例如`@hour(datetime)`。如某参数必填可加*前缀，例如`@dateAdd(*datepart,*number,*date)`
- `clazz`-实现类路径，如`com.actionsoft.apps.poc.plugin.at.MyLenExpression`
- `title`-简要说明，简明扼要
- `desc`-详细说明

### 代码示例

####   继承```AbstExpression```抽象类。

```java
public class MyIntervalEndTime extends AbstExpression {

    private final static String DATE_FORMAT = "yyyy-MM-dd";

    public MyIntervalEndTime(ExpressionContext atContext, String expressionValue) {
        super(atContext, expressionValue);
    }

    @Override
    public String execute(String expression) throws AWSExpressionException {
        // 取第1个参数 开始时间
        String startDateStr = getParameter(expression, 1);
        if (StringUtils.isBlank(startDateStr)) {
            return "";
        }
        //获取月份
        String monthStr = getParameter(expression, 2);
        Date startDate = null;
        try {
            startDate = DateUtils.parseDate(startDateStr, DATE_FORMAT);
        } catch (ParseException e) {
            LogAPI.getLogger(this.getClass()).error("时间转换异常", e);
        }
        //计算结束日期
        assert startDate != null;
        Date endDate = DateUtils.addMonths(startDate, Integer.parseInt(monthStr));

        return DateFormatUtils.format(endDate, DATE_FORMAT);

    }
}
```

#### 注册到该应用的`PluginListener`类

```java
public class Plugins implements PluginListener {
    @Override
    public List<AWSPluginProfile> register(AppContext appContext) {
        // 存放本应用的全部插件扩展点描述
        List<AWSPluginProfile> list = new ArrayList<>();
        // 注册AT公式
        list.add(new AtFormulaPluginProfile("合同台账", "@MyIntervalEndTime(*startTime,*month)", MyIntervalEndTime.class.getName(), "合同结束日期", "根据合同的开始日期和月份计算合同的结束日期"));
        return list;
    }
}
```

#### 配置插件

 	注意：在AWS CONSOLE的`应用管理 > 应用开发 > 配置应用`或AWS Developer中配置该App的`扩展插件`选项为`com.actionsoft.apps.poc.plugin.Plugins`

