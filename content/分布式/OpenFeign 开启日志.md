---
created: 2024-06-30 19:22
modified: 2024-07-06 11:06
tags: [分布式]
---

Feign 为每一个 Feign 都提供了一个 `fegin.Logger` 实例。可以在配置中开启日志输出，开启的步骤也很简单。

1. 在配置文件中配置日志级别。

```yml
logging:
  level:
    # 指定哪个 FeignClient 接口的请求需要输出日志，开启 DEBUG 日志级别
    com.learn.openfeign.service: debug
```

2.通过 Java 代码的方式在主程序入口配置日志 Bean

```java
@Configuration
public class FeignConfig {
    @Bean
    Logger.Level feignLoggerLevel() {
        return Logger.Level.FULL;
    }
}
```

`Logger.Level` 的具体级别如下：  
- `NONE`：不记录任何信息  
- `BASIC`：仅记录请求方法、URL 以及响应状态码和执行时间  
- `HEADERS`：除了记录 BASIC 级别的信息外，还会记录请求和响应的头信息  
- `FULL`：记录所有请求与响应的明细，包括头信息、请求体、元数据

> [!link] Reference
> [Spring Cloud OpenFeign](https://docs.spring.io/spring-cloud-openfeign/docs/3.1.5/reference/html/#feign-logging)
