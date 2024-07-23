---
created: 2024-06-29 11:15
modified: 2024-07-04 23:20
tags: [分布式]
---

### 什么是 OpenFeign

OpenFeign 组件的前身是 Netflix Feign 项目，它最早是作为 Netflix OSS 项目的一部分，由 Netflix 公司开发。后来 Feign 项目被贡献给了开源组织，于是才有了我们今天使用的 Spring Cloud OpenFeign 组件。

Feign 和 OpenFeign 有很多大同小异之处，不同的是 OpenFeign 支持 MVC 注解。

**可以认为 OpenFeign 为 Feign 的增强版**。

简单总结下 OpenFeign 能用来做什么：

- OpenFeign 是声明式的 HTTP 客户端，让远程调用更简单。
    
- 提供了 HTTP 请求的模板，编写简单的接口和插入注解，就可以定义好 HTTP 请求的参数、格式、地址等信息。
    
- 整合了 Ribbon（负载均衡组件）和 Hystix（服务熔断组件），不需要显示使用这两个组件。
    
- Spring Cloud Feign 在 Netflix Feign 的基础上扩展了对 SpringMVC 注解的支持。

### 工作流程

添加 `@EnableFeignClients` 注解开启对 `@FeignClient` 注解的扫描加载处理。根据 Feign Client 的开发规范，定义接口并添加 `@FeiginClient` 注解。

当程序启动之后，会进行包扫描，扫描所有 `@FeignClient` 注解的接口，并将这些信息注入到 IOC 容器中。当定义的 Feign 接口被调用时，通过 JDK 的代理的方式生成具体的 *RequestTemplate*。Feign 会为每个接口方法创建一个 RequestTemplate 对象。该对象封装了 HTTP 请求需要的所有信息，例如请求参数名、请求方法等信息。

然后由 RequestTemplate 生成 Request，把 Request 交给 Client 去处理，这里的 Client 可以是 JDK 原生的 URLConnection、HttpClient 或 Okhttp。最后 Client 被封装到 `LoadBalanceClient` 类，看这个类的名字既可以知道是结合 Ribbon 负载均衡发起服务之间的调用，因为在 OpenFeign 中默认是已经整合了 *Ribbon* 了。

### 快速集成

[[OpenFeign 3.1.5]]

### 版本升级

[[OpenFeign 3.x 升级到 4.x]]

### 维护通知

> [!news] OpenFeign 维护通知
> 由于 Spring 现在提供了自己的接口 [HTTP 客户端](https://docs.spring.io/spring-framework/reference/integration/rest-clients.html#rest-http-interface%5BSpring) 解决方案，从 2022.0.0 开始，我们将 Spring Cloud OpenFeign 视为功能完整。这意味着 Spring Cloud 团队将不再向模块添加新功能。我们仍将修复错误和安全问题，我们还将考虑和审查来自社区的小型拉取请求。
> 
> [Spring Cloud 2022.0.0  (codename Kilburn) Has Been Released](https://spring.io/blog/2022/12/16/spring-cloud-2022-0-0-codename-kilburn-has-been-released#spring-cloud-openfeign-feature-complete-announcement%5BSpring)

### 参考链接

> [!link] Reference
> [Spring Cloud OpenFeign 官网](https://spring.io/projects/spring-cloud-openfeign)
> - [Common application properties](https://docs.spring.io/spring-cloud-openfeign/docs/3.1.9/reference/html/appendix.html)
> 
> [历史文档](https://docs.spring.io/spring-cloud-openfeign/docs/)
> 
> [Spring Cloud OpenFeign 基础入门](https://mp.weixin.qq.com/s/V5P1tTI1jHNKR9P3gkFaug)
> 
> [OpenFeign 万字教程详解](https://mp.weixin.qq.com/s/3mtwIc0GiwXMCNPIJV8ONQ)
> 
> [Spring Cloud OpenFeign 进阶实战](https://mp.weixin.qq.com/s?__biz=MzkwMTY0NTkyNA==&mid=2247483794&idx=1&sn=053e427067e0da555782047b9e8f0f4e&cur_album_id=3378918985307750402#wechat_redirect)
> 
> [Spring Cloud OpenFeign 入门和实战](https://segmentfault.com/a/1190000039889836)
> 
> [深入理解 OpenFeign 的架构原理](https://mp.weixin.qq.com/s/7EJTSw5WGE5bYbo00nZ4jA)
