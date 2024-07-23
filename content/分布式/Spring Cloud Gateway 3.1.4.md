---
created: 2024-07-01 00:00
modified: 2024-07-01 00:00
tags:
  - 分布式
---

| 依赖                   | 版本         |
| -------------------- | ---------- |
| JDK                  | 17         |
| Spring Boot          | 2.6.13     |
| Spring Cloud         | 2021.0.5   |
| Spring Cloud Alibaba | 2021.0.6.0 |

### 1. 前置准备

#### 1.1 引入依赖

gateway
```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
```

nacos 注册中心
```xml
<dependency>
	<groupId>com.alibaba.cloud</groupId>
	<artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

loadbalancer
```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
```

> [!bug] 踩坑记录
> 这个坑耽误了我 3 小时，找了多篇教程，尝试了各种方法，最后引入 [[LoadBalancer]] 依赖才成功（所有教程都没有告诉我要引入这个依赖）
> 
> 调试期间，HTTP 一直报 503，控制台 debug 日志没有提供有用信息。心情很 down，今天能调通吗。
> 
> 我看到 `lb://provider` 前面的 `lb`，又联想到新版 [[OpenFeign]] 需要引入 LoadBalancer 才能使用，于是才找到正确的方法。

> 要注意不要引入 spring-boot-starter-web，会和 *WebFlux* 冲突。

#### 1.2 配置文件

application.yml

```yml
server:
  port: 8080
spring:
  application:
    name: gateway
  config:
    import: # 引入其他配置
      - application-routers.yml

  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848 # Nacos Server 地址
logging:
  level:
    org.springframework.cloud.gateway: DEBUG
```

application-routers.yml

```yml
spring:
  cloud:
    gateway:
      routes:
        - id: provider_9001
          uri: lb://provider
          predicates:
            - Path=/api/provider/**
          filters:
#            - StripPrefix=1
            - RewritePath=/api/(?<segment>.*),/$\{segment}
```

*StripPrefix* 过滤器，从请求路径中移除最前面的一个路径段。例如，如果请求路径是 /api/provider/serviceA，则转发到后端服务时路径变为 /provider/serviceA

### 2. Predicates



### 3. GatewayFilter

#### 重写路径 RewritePath

```yml
spring:
  cloud:
    gateway:
      routes:
      - id: rewritepath_route
        uri: https://example.org
        predicates:
        - Path=/red/**
        filters:
        - RewritePath=/red/?(?<segment>.*), /$\{segment}
```

- *predicates*
	- 匹配所有以 `/red/` 开头的请求路径
- *filters*
	- `/red/?` 匹配以 `/red/` 开头的任何路径。
	- `(?<segment>…)` 是一个命名捕获组的开始，其中 `segment` 是捕获组的名字。这意味着接下来的正则表达式模式匹配的内容会被捕获，并可以后续通过这个名字引用。
	- `.*`：`.`代表任意单个字符（除了换行符），`*`表示前面的元素可以重复0次或无限次。因此，`.*`整体匹配任意数量的任意字符，直到字符串的结束或下一个路径分隔符（假设路径分隔符不是`.`）。
	- `/$\{segment}` 是替换后的路径模板（由于 YAML 规范， `$` 应将其替换为 `$\` ），引用了前面捕获的 `segment` 组的值。
	- *最终效果*：任何原本以 `/red/` 开头的请求路径都会被重写，去除 `/red/` 前缀，仅保留后面的部分，然后转发到目标服务。

### 4. 动态路由

#todo

> [!todo] 难搞哦
> 代码参考了链接 1 和 2，通过 [actuator](http://127.0.0.1:8080/actuator/gateway/routes) 可以看到读取不到 `shared-routes.json`。感觉动态路由好复杂哦，暂时搞不出来，有时间再看吧。
> 1. [SpringCloud Gateway+Nacos，1000+路由规则如何动态实现？](https://mp.weixin.qq.com/s/-0bs9trfI5hlMW3LnW1i9g)
> 2. [Spring Cloud Gateway + Nacos 灰度发布](https://mp.weixin.qq.com/s/_aCI8ooCtEewy7jE6ZSpdg)
> 3. [Spring Cloud Gateway 之动态路由](https://mp.weixin.qq.com/s/dsgPkVqc5UtqaitiFY--Qg)
> 4. [Spring Cloud + Nacos + 负载均衡器实现全链路灰度发布实战](https://mp.weixin.qq.com/s/CXrIQkRi2Y1Yqml8GNaXIw)

> [!link] Reference
> 
> [Spring Cloud Gateway](https://docs.spring.io/spring-cloud-gateway/docs/3.1.9/reference/html/)
> 
> [Spring Cloud Gateway 简单入门，强大的微服务网关 | 南瓜慢说知识库](https://www.pkslow.com/docs/zh/spring-cloud-gateway-introduction/)
> 
> [SpringCloud Gateway + Nacos，1000+路由规则如何动态实现？](https://mp.weixin.qq.com/s/-0bs9trfI5hlMW3LnW1i9g)
