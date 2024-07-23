---
created: 2024-06-30 23:04
modified: 2024-07-06 09:15
tags: [分布式]
---

### 什么是 Spring Cloud Gateway

**Spring Cloud Gateway** 属于 [[Spring Cloud]] 生态系统中的*网关*组件，负责路由请求到不同的后端服务。

最开始 Spring Cloud 推荐的网关是 *Netflix Zuul 1.x*，但是停止维护了。尽管后来有 Zuul 2.0，但是因为开发延期比较严重，Spring Cloud 官方自己开发了 Spring Cloud Gateway 用于代替 Zuul 网关。

为了提升网关的性能，Spring Cloud Gateway 基于 Spring WebFlux 。Spring WebFlux 使用 Reactor 库来实现响应式编程模型，底层基于 Netty 实现同步非阻塞的 I/O。

### 术语

**路由**（Route）：网关的基本构建单元。它由一个 ID、一个目标 URI、一组谓词（Predicates）和一组过滤器（Filters）定义。如果整体谓词为真，则匹配该路由。

**断言**（[[Spring Cloud Gateway Predicate|Predicate]]）：这是一个 Java 8 的函数式接口 Predicate。它的输入类型是 *Spring* 框架的 [ServerWebExchange](https://docs.spring.io/spring-framework/docs/5.0.x/javadoc-api/org/springframework/web/server/ServerWebExchange.html)。它允许你根据 HTTP 请求中的任何内容进行匹配，比如请求头（headers）或参数（parameters）。

**过滤器**（Filter）：这些是使用特定工厂构建的 GatewayFilter 实例。在这里，你可以在向下游发送请求之前或之后修改请求和响应。过滤器允许你在请求处理的各个阶段插入自定义逻辑，比如添加、删除或修改请求头，修改响应内容，甚至完全终止请求处理流程。

### 工作流程

![[Pasted image 20240701141226.png]]

① **路由判断**：客户端的请求到达网关后，先经过 *Gateway Handler Mapping* 处理，这里面会做断言（*Predicate*）判断，看下符合哪个路由规则，这个路由映射后端的某个服务。

② **请求过滤**：然后请求到达 *Gateway Web Handler*，这里面有很多过滤器，组成过滤器链（*Filt| Header     | 请求必须包含某些 header    | 头、参数校验等等，然后将请求转发到实际的后端服务。这些过滤器逻辑上可以称作 *Pre-Filters*。

③ **服务处理**：后端服务会对请求进行处理。

④ **响应过滤**： 后端处理完结果后，返回给 Gateway 的过滤器再次做处理，逻辑上可以称作 *Post-Filters*。

⑤ **响应返回**：响应经过过滤器处理后，返回给客户端。

最主要就是做了两件事：

1. 哪些请求可以被它处理，由 `Predicate` 决定；
2. 如何处理，由 `Filter` 决定。

### 快速集成

[[Spring Cloud Gateway 3.1.4]]

[[Spring Cloud Gateway 3.0.2]]

> [!link] Reference
> 
> [Spring Cloud Gateway](https://spring.io/projects/spring-cloud-gateway)
> 
> [历史文档](https://docs.spring.io/spring-cloud-gateway/docs/)
