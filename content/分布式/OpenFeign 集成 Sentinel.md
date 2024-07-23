---
created: 2024-07-04 18:17
modified: 2024-07-04 22:32
tags: [分布式]
cssclasses:
  - embed-strict
---

### Version

![[cloud-alibaba-2024 版本控制|seamless]]

### 1. 引入依赖

sentinel
```xml
<dependency>
	<groupId>com.alibaba.cloud</groupId>
	<artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

其他依赖
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
	<groupId>com.alibaba.cloud</groupId>
	<artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>

<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>

<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
```

### 2. 添加配置

**配置控制台信息**
```yml
spring:
  cloud:
    sentinel:
      transport:
        port: 8091
        dashboard: 127.0.0.1:8090
```

这里的 `spring.cloud.sentinel.transport.port` 端口配置会在应用对应的机器上启动一个 Http Server，该 Server 会与 Sentinel 控制台做交互。比如 Sentinel 控制台添加了一个限流规则，会把规则数据 push 给这个 Http Server 接收，Http Server 再将规则注册到 Sentinel 中。

更多 Sentinel 控制台的使用及问题参考： [Sentinel 控制台文档](https://github.com/alibaba/Sentinel/wiki/%E6%8E%A7%E5%88%B6%E5%8F%B0) 以及 [Sentinel FAQ](https://github.com/alibaba/Sentinel/wiki/FAQ)

**开启 Feign 支持**

`feign.sentinel.enabled=true`

```yml
feign:
  sentinel:
    enabled: true
```

> [!note]
> Feign 对应的接口中的资源名策略定义：httpmethod:protocol://requesturl。`@FeignClient` 注解中的所有属性，Sentinel 都做了兼容。

**application.yml**
```yml
server:
  port: 9005
spring:
  application:
    name: sentinel-consumer
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
    sentinel:
      transport:
        port: 8091
        dashboard: 127.0.0.1:8090

feign:
  sentinel:
    enabled: true
```

### 3. FeignClient

*FeignService*
```java
@FeignClient(name = "provider", path = "/provider", fallbackFactory = FeignFallbackFactory.class)
public interface FeignService {

    @GetMapping(value = "/env/getServerPort")
    String searchRepo();

}
```

*FeignFallbackFactory*
```java
@Component
public class FeignFallbackFactory implements FallbackFactory<FeignFallback> {

    @Override
    public FeignFallback create(Throwable cause) {
        return new FeignFallback();
    }
}
```

*FeignFallback*
```java
public class FeignFallback implements FeignService {
    @Override
    public String searchRepo() {
        return "fallback";
    }
}
```

### 4. 生产者

以上是消费者的代码，接下来是生产者：
[[Nacos 2.3.2 融合 Spring Cloud#创建子模块 nacos-provider-9001]]

### 5. Sentinel 控制台

请求消费者的接口，刷新控制台页面，即可看到监控信息。

![[Pasted image 20240705175158.png]]

### 踩坑记录

> [!bug] 版本一定要对应！！！
> 
> 今天下午反复调试都无法成功，原因竟是版本问题：
> - Spring Cloud 2022.0.0-RC2
> - Spring Cloud Alibaba 2022.0.0.0-RC2
> 
> 晚上根据 [[Spring Cloud Alibaba#Spring Boot 与 Spring Cloud Alibaba 版本对应关系]]，重新修改版本，其他代码无改动，测试成功！
> - Spring Cloud 2022.0.0
> - Spring Cloud Alibaba 2022.0.0.0
> 
> **启示**：版本一定要对应！！！少走弯路！

> [!link] Reference
> [Sentinel · alibaba/spring-cloud-alibaba Wiki · GitHub](https://github.com/alibaba/spring-cloud-alibaba/wiki/Sentinel)
> 
> [Spring Cloud OpenFeign](https://docs.spring.io/spring-cloud-openfeign/docs/3.1.9/reference/html/#spring-cloud-feign-circuitbreaker-fallback)
