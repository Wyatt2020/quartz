---
created: 2024-06-30 19:26
modified: 2024-06-30 19:26
tags: []
---

### 对单个特定名称的 FeignClient 进行配置

详细配置属性：
`org.springframework.cloud.openfeign.FeignClientProperties`

```yml
feign:
    client:
        config:
            # 需要配置的 FeignName
            feignName:
                connectTimeout: 5000
                readTimeout: 5000
                loggerLevel: full
                errorDecoder: com.example.SimpleErrorDecoder
                retryer: com.example.SimpleRetryer
                defaultQueryParameters:
                    query: queryValue
                defaultRequestHeaders:
                    header: headerValue
                requestInterceptors:
                    - com.example.FooRequestInterceptor
                    - com.example.BarRequestInterceptor
                decode404: false
                encoder: com.example.SimpleEncoder
                decoder: com.example.SimpleDecoder
                contract: com.example.SimpleContract
                capabilities:
                    - com.example.FooCapability
                    - com.example.BarCapability
                queryMapEncoder: com.example.SimpleQueryMapEncoder
                metrics.enabled: false

```

### 作用于所有 FeignClient 的配置

在 *@EnableFeignClients* 注解上有一个 *defaultConfiguration* 属性，可以将默认设置写成一个配置类，例如这个类叫做 *DefaultFeignClientConfiguration*。

```java
@SpringBootApplication
@EnableFeignClients(defaultConfiguration = DefaultFeignClientConfiguration.class)
public class FeignClientConfigApplication{
    SpringApplication.run(FeignClientConfigApplication.class, args);
}
```

同时也可以在配置文件中配置：

```yml
feign:
  client:
    config:
      default:
        # 连接超时时间
        connectTimout: 5000
        # 读超时时间
        readTimeut: 5000
        # Feign的日志级别
        loggerLevel: full
        ...
```

如果以上两种方式(在配置文件和在注解中配置 FeignClient 的全局配置)，最后配置文件会覆盖注解上执行配置类的方式。

但是可以在配置文件中添加 `feign.client.default-to-properties=false` 来改变 Feigin 配置的优先级。

> [!link] Reference
> [Spring Cloud OpenFeign](https://docs.spring.io/spring-cloud-openfeign/docs/3.1.9/reference/html/#spring-cloud-feign-overriding-defaults)
