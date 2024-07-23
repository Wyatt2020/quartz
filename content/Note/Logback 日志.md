---
created: 2024-06-29 18:14
modified: 2024-06-29 18:22
tags:
  - SpringBoot
---

### 什么是 Logback

Logback 是由 log4j 创始人设计的另一个开源日志组件，*Spring Boot* 默认集成了 Logback，并用 *INFO* 级别输出到控制台。

官方网站： [http://logback.qos.ch](http://logback.qos.ch)

> Logback 就是一个更好的 log4j 1.x
> via：[Reasons to prefer logback](https://logback.qos.ch/reasonsToSwitch.html)

### 配置文件命名规则

根据不同的日志框架，你可以按如下规则组织配置文件名，就能被正确加载：

| 日志框架                    | 配置文件名                                                                    |
| ----------------------- | ------------------------------------------------------------------------ |
| Logback                 | `logback-spring.xml`, `logback-spring.groovy`, `logback.xml`, `logback.groovy` |
| Log4j                   | `log4j-spring.properties`, `log4j-spring.xml`, `log4j.properties`, `log4j.xml`   |
| Log4j2                  | `log4j2-spring.xml`, `log4j2.xml`                                            |
| JDK (Java Util Logging) | `logging.properties`                                                       |

Spring Boot 官方推荐优先使用带有 `-spring` 的文件名作为你的日志配置，命名为 `logback-spring.xml` 的日志配置文件，Spring Boot 可以为它添加一些 Spring Boot 特有的配置项。

默认的命名规则，并且放在 `src/main/resources` 下面即可，注意：`logback.xml` 加载早于 `application.yml`

#### 别名配置

如果使用的是别的配置名称，则需要在 Spring Boot 的 `application.yml` 配置文件中经行配置：

```yaml
# 日志配置  
logging:  
  config: classpath:mylogback.xml
```

### Logback 内置 Slf4j

![[Pasted image 20240721095700.png]]

根据上图我们可以看到，`spring-boot-starter-web` 内置了 Logback，而 Logback 又内置了 `slf4j-api`，所以无需引入 `slf4j-api` 即可使用 Slf4j

### 日志级别排序

*TRACE* < *DEBUG* < *INFO* < *WARN* < *ERROR*

- 配置 error 打印 error
- 配置 warn 打印 warn、error
- 以此类推

### 文件输出配置

每一天的日志归档到一个文件中

```xml
<appender name="FILE_LOG" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <!--日志名称，如果没有File 属性，那么只会使用FileNamePattern的文件路径规则
        如果同时有<File>和<FileNamePattern>，那么当天日志是<File>，明天会自动把今天
        的日志改名为今天的日期。即，<File> 的日志都是当天的。
    -->
    <File>${logPath}/${appName}.log</File>
    <!--滚动策略，按照时间滚动 TimeBasedRollingPolicy-->
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
        <!--文件路径,定义了日志的切分方式——把每一天的日志归档到一个文件中,防止日志填满整个磁盘空间-->
        <FileNamePattern>${logPath}/${appName}.%d{yyyy-MM-dd}.log</FileNamePattern>
        <!--只保留最近30天的日志-->
        <maxHistory>30</maxHistory>
        <!--用来指定日志文件的上限大小，如果此<rollingPolicy/>下所有日志文件的总大小空间到了这个值，就会删除旧的日志-->
        <totalSizeCap>1GB</totalSizeCap>
    </rollingPolicy>
    <!--日志输出编码格式化-->
    <encoder>
        <charset>UTF-8</charset>
        <pattern>%d [%thread] %-5level %logger{36} %line - %msg%n</pattern>
    </encoder>
</appender>
```

如果想压缩历史 log 文件，可以在 `FileNamePattern` 加上 `.zip` 后缀

```xml
<FileNamePattern>${logPath}/${appName}.%d{yyyy-MM-dd}.log.zip</FileNamePattern>
```

如果想指定单个文件大小上限，可以在 `rollingPolicy` 下添加 `timeBasedFileNamingAndTriggeringPolicy` 节点，并且在 `FileNamePattern` 加上 `%i` 指定文件的计数。

```xml
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
        <!--文件路径,定义了日志的切分方式——把每一天的日志归档到一个文件中,防止日志填满整个磁盘空间-->
        <FileNamePattern>${logPath}/${appName}.%d{yyyy-MM-dd}.%i.log</FileNamePattern>
        <!--设置日志文件大小，超过就重新生成文件，默认10M-->
        <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
            <maxFileSize>100MB</maxFileSize>
        </timeBasedFileNamingAndTriggeringPolicy>
    </rollingPolicy>
```

![[Pasted image 20240721095713.png]]

> [!link] Reference
> [Logback 简介与配置详解](https://mp.weixin.qq.com/s/oNx_dPEuP2UVJMDwvF8W5w)
> 
> [Spring Boot + Logback 详细配置](https://blog.csdn.net/qq_43144822/article/details/138704775)
> 
> [Logback 的使用和 logback.xml 详解](https://www.cnblogs.com/ryelqy/p/10314147.html)
> 
> [Logback 详解](https://blog.csdn.net/qq_36850813/article/details/108569093)
> 
> [Logback 日志，输出到文件以及实时输出到 Web 页面](https://www.cnblogs.com/huanzi-qch/p/11041300.html)
