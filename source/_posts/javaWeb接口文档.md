---
layout: java
title: javaWeb接口文档
date: 2020-12-09 16:20:03
tags: java
categories: Java
keywords: smart-doc
cover: false
---

### 传统swagger（之前在用）接口文档的缺点：

1、代码侵入性太强。

2、写着麻烦。需要写大量的注解，太麻烦！

### smart-doc的优点：

1、不需要注解，无侵入性。

2、只需要写好注释即可，界面也比较美观。

3、对一些常用的电话、地址之类的模拟的数据跟真的一样（哈哈哈）。

4、可以生成Markdown、HTML5等多种文档格式。

以下是官方对其描述的一些特性：

**零注解、零学习成本、只需要写标准java注释。**

**基于源代码接口定义自动推导，强大的返回结构推导。**

**支持Spring MVC,Spring Boot,Spring Boot Web Flux(controller书写方式)。**

**支持Callable,Future,CompletableFuture等异步接口返回的推导。**

**支持JavaBean上的JSR303参数校验规范。 对json请求参数的接口能够自动生成模拟json参数。**

**对一些常用字段定义能够生成有效的模拟值。**

**支持生成json返回值示例。**

**支持从项目外部加载源代码来生成字段注释(包括标准规范发布的jar包)。**

**支持生成多种格式文档：Markdown、HTML5、Asciidoctor、Postman Collection。**

**轻易实现在Spring Boot服务上在线查看静态HTML5 api文档。**

**开放文档数据，可自由实现接入文档管理系统。**

**支持生成dubbo rpc文档。**

### 使用示例

### 一、maven插件（推荐）

#### 1、引入依赖

```java
<plugin>
                <groupId>com.github.shalousun</groupId>
                <artifactId>smart-doc-maven-plugin</artifactId>
                <version>1.1.9</version>
                <configuration>
                    <!--指定生成文档的使用的配置文件,配置文件放在自己的项目中-->
                    <configFile>./src/main/resources/smart-doc.json</configFile>
                    <!--指定项目名称-->
                    <projectName>UU跑腿广告服务中心文档</projectName>
                </configuration>
                <executions>
                    <execution>
                        <goals>
                            <goal>html</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
```

#### 2、添加smart-doc生成文档的配置

```json
{
  "serverUrl": "http://127.0.0.1", //设置服务器地址,非必须
  "isStrict": false, //是否开启严格模式
  "allInOne": true,  //是否将文档合并到一个文件中，一般推荐为true
  "outPath": "D://md2", //指定文档的输出路径
  "coverOld": true,  //是否覆盖旧的文件，主要用于mardown文件覆盖
  "packageFilters": "",//controller包过滤，多个包用英文逗号隔开
  "md5EncryptedHtmlName": false,//只有每个controller生成一个html文件是才使用
  "projectName": "smart-doc",//配置自己的项目名称
  "skipTransientField": true,//目前未实现
  "showAuthor":true,//是否显示接口作者名称，默认是true,不想显示可关闭
  "requestFieldToUnderline":true, //自动将驼峰入参字段在文档中转为下划线格式,//@since 1.8.7 版本开始
  "responseFieldToUnderline":true,//自动将驼峰入参字段在文档中转为下划线格式,//@since 1.8.7 版本开始
  "inlineEnum":true,//设置为true会将枚举详情展示到参数表中，默认关闭，//@since 1.8.8版本开始
  "recursionLimit":7,//设置允许递归执行的次数用于避免栈溢出，默认是7，正常为3次以内，//@since 1.8.8版本开始
  "ignoreRequestParams":[ //忽略请求参数对象，把不想生成文档的参数对象屏蔽掉，@since 1.9.2
      "org.springframework.ui.ModelMap"
  ],
  "dataDictionaries": [ //配置数据字典，没有需求可以不设置
    {
      "title": "http状态码字典", //数据字典的名称
      "enumClassName": "com.power.common.enums.HttpCodeEnum", //数据字典枚举类名称
      "codeField": "code",//数据字典字典码对应的字段名称
      "descField": "message"//数据字典对象的描述信息字典
    }
  ],

  "errorCodeDictionaries": [{ //错误码列表，没有需求可以不设置
    "title": "title",
    "enumClassName": "com.power.common.enums.HttpCodeEnum", //错误码枚举类,如果是枚举是在一个类中定义则用$链接类BaseErrorCode$Common
    "codeField": "code",//错误码的code码字段名称
    "descField": "message"//错误码的描述信息对应的字段名
  }],

  "revisionLogs": [ //设置文档变更记录，没有需求可以不设置
    {
      "version": "1.0", //文档版本号
      "status": "update", //变更操作状态，一般为：创建、更新等
      "author": "author", //文档变更作者　　　　"revisionTime": "2020-09-24", //变更时间
      "remarks": "desc" //变更描述
    }
  ],
  "customResponseFields": [ //自定义添加字段和注释，api-doc后期遇到同名字段则直接给相应字段加注释，非必须
    {
      "name": "code",//覆盖响应码字段
      "desc": "响应代码",//覆盖响应码的字段注释
      "value": "00000"//设置响应码的值
    }
  ],
  "requestHeaders": [ //设置请求头，没有需求可以不设置
    {
      "name": "token",
      "type": "string",
      "desc": "desc",
      "required": false,
      "value":"55",
      "since": "-"
    }
  ],
  "rpcApiDependencies":[{ // 项目开放的dubbo api接口模块依赖，配置后输出到文档方便使用者集成
      "artifactId":"SpringBoot2-Dubbo-Api",
      "groupId":"com.demo",
      "version":"1.0.0"
  }],
  "rpcConsumerConfig":"src/main/resources/consumer-example.conf",//文档中添加dubbo consumer集成配置，用于方便集成方可以快速集成
  "apiObjectReplacements": [{ // 自smart-doc 1.8.5开始你可以使用自定义类覆盖其他类做文档渲染，使用全类名
      "className": "org.springframework.data.domain.Pageable",
      "replacementClassName": "com.power.doc.model.PageRequestDto" //自定义的PageRequestDto替换Pageable做文档渲染
  }],
  "apiConstants": [{//从1.8.9开始配置自己的常量类，smart-doc在解析到常量时自动替换为具体的值,如：http://localhost:8080/testConstants/+ApiVersion.VERSION中的ApiVersion.VERSION会被替换
      "constantsClassName": "com.power.doc.constants.RequestParamConstant"
  }],
  "sourceCodePaths": [ //设置代码路径，smart-doc默认会自动加载src/main/java, 没有需求可以不设置 1.0.0以后版本此配置不再生效
    {
      "path": "src/main/java",
      "desc": "测试"
    }
  ]
}
```

#### 3、运行插件生成文档

![img](https://img2020.cnblogs.com/blog/1171252/202009/1171252-20200924133536412-1092390809.png)

最后通过ip和端口号访问即可。

 

### 二、测试类方式

#### 1、引入依赖

```java
<dependency>
            <groupId>com.github.shalousun</groupId>
            <artifactId>smart-doc</artifactId>
            <version>1.8.7</version>
            <scope>test</scope>
        </dependency>
```

#### 2、每个项目建议都写一个测试类

```java
import com.power.common.util.DateTimeUtil;
import com.power.doc.builder.ApiDocBuilder;
import com.power.doc.builder.HtmlApiDocBuilder;
import com.power.doc.constants.DocGlobalConstants;
import com.power.doc.model.*;
import lombok.extern.slf4j.Slf4j;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;


/**
 * @author qyy
 * @title: SpringBootTest
 * @projectName pt
 * @description: api文档生成测试
 * @date 2020/1/7 000710:15
 */
@RunWith(SpringRunner.class)
@SpringBootTest
@Slf4j
public class SpringBootTest {


    @Test
    public void testBuilderMdControllersApi() {
        ApiConfig config = new ApiConfig();
        config.setServerUrl("http://localhost:8080");

        //设置为严格模式，Smart-doc将降至要求每个Controller暴露的接口写上标准文档注释
        //config.setStrict(true);
        config.setStrict(false);
        //当把AllInOne设置为true时，Smart-doc将会把所有接口生成到一个Markdown、HHTML或者AsciiDoc中
        config.setAllInOne(true);

        //Set the api document output path.
        config.setOutPath("d:\\md");

        // 设置接口包扫描路径过滤，如果不配置则Smart-doc默认扫描所有的接口类
        // 配置多个报名有英文逗号隔开
        // config.setPackageFilters("com.power.doc.controller");

        //since 1.7.5
        //如果该选项的值为false,则smart-doc生成allInOne.md文件的名称会自动添加版本号
        config.setCoverOld(true);
        //since 1.7.5
        //设置项目名(非必须)，如果不设置会导致在使用一些自动添加标题序号的工具显示的序号不正常
        config.setProjectName("XXXXX接口文档");

        //设置公共请求头.如果不需要请求头，则无需设置
        config.setRequestHeaders(
                ApiReqHeader.header().setName("access_token").setType("string")
                        .setDesc("Basic auth credentials").setRequired(true).setSince("v1.1.0"),
                ApiReqHeader.header().setName("user_uuid").setType("string").setDesc("User Uuid key")
        );

        long start = System.currentTimeMillis();
        //生成Markdown文件
        ApiDocBuilder.buildApiDoc(config);

        long end = System.currentTimeMillis();
        DateTimeUtil.printRunTime(end, start);
    }


    @Test
    public void testBuilderHtmlControllersApi() {
        ApiConfig config = new ApiConfig();
        config.setServerUrl("http://192.168.6.110:8080");

        //设置为严格模式，Smart-doc将降至要求每个Controller暴露的接口写上标准文档注释
        config.setStrict(false);

        //当把AllInOne设置为true时，Smart-doc将会把所有接口生成到一个Markdown、HHTML或者AsciiDoc中
        config.setAllInOne(true);

        //HTML5文档，建议直接放到src/main/resources/static/doc下，Smart-doc提供一个配置常量HTML_DOC_OUT_PATH
        config.setOutPath(DocGlobalConstants.HTML_DOC_OUT_PATH);

        config.setProjectName("XXXXXX接口文档");

        // 设置接口包扫描路径过滤，如果不配置则Smart-doc默认扫描所有的接口类
        // 配置多个报名有英文逗号隔开
        //config.setPackageFilters("com.power.doc.controller");

        //设置公共请求头.如果不需要请求头，则无需设置
//        config.setRequestHeaders(
//                ApiReqHeader.header().setName("access_token").setType("string")
//                        .setDesc("Basic auth credentials").setRequired(true).setSince("v1.1.0"),
//                ApiReqHeader.header().setName("user_uuid").setType("string").setDesc("User Uuid key")
//        );

        //设置错误错列表，遍历自己的错误码设置给Smart-doc即可
//        List<ApiErrorCode> errorCodeList = new ArrayList<>();
//        for (ErrorCodeEnum codeEnum : ErrorCodeEnum.values()) {
//            ApiErrorCode errorCode = new ApiErrorCode();
//            errorCode.setValue(codeEnum.getCode()).setDesc(codeEnum.getDesc());
//            errorCodeList.add(errorCode);
//        }
//        //不需要显示错误码，则可以设置
//        config.setErrorCodes(errorCodeList);


        long start = System.currentTimeMillis();
        //生成HTML5文件
        HtmlApiDocBuilder.buildApiDoc(config);

        long end = System.currentTimeMillis();
        DateTimeUtil.printRunTime(end, start);
    }

}
```

#### 3、测试生成接口文档。

我这边主要是生成的html接口文档，生成之后会在项目`\src\main\resources\static\doc`这个目录下，可以直接在浏览器输入`http://192.168.6.110:8080/doc/index.html#`即可访问。

#### 4、注释说明

一定要写java注释,注释如下参考：

```java
/**
     * id
     */
    private Integer id;
```

#### 5、效果请查看官网所示

[https://gitee.com/smart-doc-team/smart-doc/wikis/%E6%96%87%E6%A1%A3%E6%95%88%E6%9E%9C%E5%9B%BE?sort_id=1652819](https://gitee.com/smart-doc-team/smart-doc/wikis/文档效果图?sort_id=1652819)