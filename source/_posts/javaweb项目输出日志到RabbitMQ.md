---
title: javaweb项目输出日志到RabbitMQ
date: 2021-08-02 21:49:55
tags:
- Java
- 日志
- RabbitMQ
categories: 
- Java
cover: false
---

## 自定义Appender继承UnsynchronizedAppenderBase

```java
import ch.qos.logback.classic.Level;
import ch.qos.logback.classic.spi.ILoggingEvent;
import ch.qos.logback.classic.spi.IThrowableProxy;
import ch.qos.logback.classic.spi.ThrowableProxy;
import ch.qos.logback.core.Layout;
import ch.qos.logback.core.UnsynchronizedAppenderBase;
import com.alibaba.fastjson.JSONObject;
import com.xiaoleilu.hutool.exceptions.ExceptionUtil;
import com.xiaoleilu.hutool.system.SystemUtil;
import lombok.Getter;
import lombok.Setter;
import org.springframework.amqp.rabbit.connection.CachingConnectionFactory;
import org.springframework.amqp.rabbit.core.RabbitTemplate;

import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Locale;

/**
 * @author qyf
 * @title: LogbackMQAppender
 * @projectName 
 * @description: 自定义appender实现往rabbitmq写日志
 * @date 2020/7/2 000214:33
 */
@Getter
@Setter
public class LogbackMQAppender extends UnsynchronizedAppenderBase<ILoggingEvent> {

    private CachingConnectionFactory connectionFactory = null;
    private RabbitTemplate template;

    /**
     * 应用名
     */
    String appName;
    /**
     * 地址
     */
    String host;
    /**
     * 端口
     */
    int port;
    /**
     * 用户名
     */
    String username;

    /**
     * 密码
     */
    String password;

    Layout<ILoggingEvent> layout;

    @Override
    public void start() {
        //这里可以做些初始化判断 比如layout不能为null ,
        if (layout == null) {
            addWarn("Layout was not defined");
        }
        connectionFactory = new CachingConnectionFactory(host, port);
        connectionFactory.setUsername(username);
        connectionFactory.setPassword(password);
        template = new RabbitTemplate(connectionFactory);
        super.start();
    }


    @Override
    public void stop() {
        if (!isStarted()) {
            return;
        }
        template.destroy();
        connectionFactory.destroy();
        super.stop();
    }

    @Override
    protected void append(ILoggingEvent event) {
        if (event == null || !isStarted()) {
            return;
        }
        RequestLog requestLog = new RequestLog();

        requestLog.setHost(SystemUtil.getHostInfo().getAddress());
        requestLog.setMachineName(SystemUtil.getHostInfo().getName());
        requestLog.setApplicationName(appName);
        requestLog.setAddTime(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS Z", Locale.CHINESE).format(new Date()));
        if (event.getLevel().levelInt == Level.ERROR_INT) {
            final IThrowableProxy throwableProxy = event.getThrowableProxy();
            if (throwableProxy == null) {
                return;
            }
            if (!(throwableProxy instanceof ThrowableProxy)) {
                return;
            }

            Throwable throwable = ((ThrowableProxy) throwableProxy).getThrowable();
            requestLog.setMessage(event.getFormattedMessage() + ",\n\n详细错误信息如下\n" + ExceptionUtil.stacktraceToString(throwable));
            requestLog.setLogLevel(LogLevel.ERROR.ordinal());
            requestLog.setStatusCode(500);
            template.convertAndSend(QueueConsts.QUEUE_ERROR_NAME, JSONObject.toJSONString(requestLog));
        } else {
            requestLog.setMessage(event.getFormattedMessage());
            requestLog.setLogLevel(LogLevel.INFORMATION.ordinal());
            requestLog.setStatusCode(200);
            template.convertAndSend(QueueConsts.QUEUE_INFO_NAME, JSONObject.toJSONString(requestLog));
        }
    }
}
```



RequestLog类

```java
import com.alibaba.fastjson.annotation.JSONField;
import com.fasterxml.jackson.annotation.JsonProperty;
import lombok.Data;
import lombok.extern.slf4j.Slf4j;

import java.io.Serializable;

/**
 * @author qyf
 * @title: RequestLog
 * @projectName 
 * @description: TODO
 * @date 2020/5/23 002309:40
 */
@Data
@Slf4j
public class RequestLog implements Serializable {

    private static final long serialVersionUID = 1L;

    private final String appId;

    private final String groupId;

    private String applicationName;

    public String machineName;

    public String addTime;


    public int getType() {
        int rst = 1;
        switch (logLevel) {
            case 0:
            case 1:
            case 2:
                rst = 1;
                break;
            case 3:
                rst = 2;
                break;
            case 4:
                rst = 0;
                break;
            case 5:
                rst = 3;
                break;
            default:
                break;
        }
        return rst;
    }

    @JSONField(name = "Type")
    public int type;

    @JSONField(name = "Host")
    public String host;

    @JSONField(name = "Url")
    public String url;

    @JSONField(name = "HttpMethod")
    public String httpMethod;

    @JSONField(name = "IpAddress")
    public String ipAddress;

    @JSONField(name = "Source")
    public String source;

    @JSONField(name = "Message")
    public String message;

    @JSONField(name = "StatusCode")
    public Integer statusCode;

//    @JSONField(name = "HttpHeader")
//    public HttpHead httpHeader;

    @JSONField(name = "Header")
    public String header;

    @JSONField(name = "Forms")
    public String forms;

    @JSONField(name = "Grade")
    public int grade;

    @JSONField(name = "LogLevel")
    public Integer logLevel;

}
```

## logback-spring.xml配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!-- constant property -->
    <springProperty scope="context" name="APP_NAME" source="spring.application.name"/>
    <springProperty scope="context" name="BASE_LOG_DIR" source="logback.base-log-dir"/>
    <springProperty scope="context" name="LOG_LEVEL" source="log.level"/>

    <springProperty scope="context" name="MQ_HOST" source="spring.rabbitmq.host"/>
    <springProperty scope="context" name="MQ_PORT" source="spring.rabbitmq.port"/>
    <springProperty scope="context" name="MQ_USERNAME" source="spring.rabbitmq.username"/>
    <springProperty scope="context" name="MQ_PASSWORD" source="spring.rabbitmq.password"/>

    <!-- color converter -->
    <conversionRule conversionWord="clr" converterClass="org.springframework.boot.logging.logback.ColorConverter"/>
    <conversionRule conversionWord="wex"
                    converterClass="org.springframework.boot.logging.logback.WhitespaceThrowableProxyConverter"/>
    <conversionRule conversionWord="wEx"
                    converterClass="org.springframework.boot.logging.logback.ExtendedWhitespaceThrowableProxyConverter"/>
    <!-- color log pattern -->
    <property name="CONSOLE_LOG_PATTERN"
              value="[${APP_NAME}] ${CONSOLE_LOG_PATTERN:-%clr(%d{yyyy-MM-dd HH:mm:ss.SSS}){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%-40.40logger{39}){cyan} %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}"/>
    <!-- console stdout appender -->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>${CONSOLE_LOG_PATTERN}</pattern>
            <charset>UTF-8</charset>
        </encoder>
    </appender>
    <appender name="amaqBack"
              class="com.qyf.log.LogbackMQAppender">
        <!--        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">-->
        <!--            &lt;!&ndash; 日志收集最低日志级别 &ndash;&gt;-->
        <!--            <level>INFO</level>-->
        <!--        </filter>-->
        <layout
                class="ch.qos.logback.classic.PatternLayout">
            <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符 -->
            <pattern>${CONSOLE_LOG_PATTERN}</pattern>
        </layout>
        <!-- 自定义参数 -->
        <appName>${APP_NAME}</appName>
        <host>${MQ_HOST}</host>
        <port>${MQ_PORT}</port>
        <username>${MQ_USERNAME}</username>
        <password>${MQ_PASSWORD}</password>
    </appender>


    <!-- root log level -->
    <root level="${LOG_LEVEL}">
        <appender-ref ref="STDOUT"/>
        <appender-ref ref="amaqBack"/>
    </root>
</configuration>
```

## 在application-dev.yml配置RabbitMQ信息

```yaml
spring:
  rabbitmq:
    host: 127.0.0.1
    port: 5672
    username: admin
    password: 123456
```
