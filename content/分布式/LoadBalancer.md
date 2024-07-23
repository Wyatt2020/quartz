---
created: 2024-07-01 18:50
modified: 2024-07-01 18:50
tags:
  - 分布式
---

*Ribbon* 是 [[Spring Cloud Netflix]] 中的一员，目前已经停止维护。**LoadBalancer** 是 Spring Cloud 官方提供的负载均衡组件，可用于替代 Ribbon。LoadBalancer 的使用方式与 Ribbon 基本兼容，可以从 Ribbon 进行平滑过渡。  

与 Ribbon 相比，LoadBalancer 支持响应式编程负载均衡，即结合 Spring Web Flux 使用，更适合处理高并发、高吞吐量的场景。不过，LoadBalancer 提供的负载均衡算法目前比 Ribbon 提供的要少一些，LoadBalancer 仅提供了轮询和随机的负载策略。如下图所示，[官网](https://docs.spring.io/spring-cloud-commons/docs/current/reference/html/#spring-cloud-loadbalancer)有描述。

### 依赖

```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
```

> [!link] Reference
> [Spring Cloud LoadBalancer 详解和基本实现](https://juejin.cn/post/7247467756245876794)
