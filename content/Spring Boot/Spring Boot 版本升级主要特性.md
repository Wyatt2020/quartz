---
created: 2024-06-30 23:38
modified: 2024-06-30 23:38
tags:
  - SpringBoot
---

#### Spring Boot 3.1

- 移除 Apache HttpClient 4，由 HttpClient 5 取代
- `ServletRegistrationBean` 和 `FilterRegistrationBean` 注册失败时抛出异常 `IllegalStateException`，而不是打警告日志
- Hibernate 升级到 6.2
- Jackson 升级到 2.15
- Mockito 升级到 5.3
- 新增 `spring-boot-docker-compose` 模块
- 新增 `Testcontainers`，方便在开发时管理外部服务
- `Spring Authorization Server` 自动配置等

#### Spring Boot 3.0

变更巨大，比较大的变化有：

- JDK 最低也要 JDK 17 了
- 更贴近云原生，使用 GraalVM 和 Native Build Tools 将应用打包成可执行文件
- 大量第三方库的升级

#### Spring Boot 2.7

- OkHttp 升级版本 4
- 新增 `spring-boot-starter-graphql`

#### Spring Boot 2.6

- `info` 提供 Java Runtime 信息

#### Spring Boot 2.5

- 支持环境变量前缀 `application.setEnvironmentPrefix("myapp")`
- HTTP/2 over TCP
- 支持 Java 16

#### Spring Boot 2.4

- Spring 5.4
- R2DBC 支持改为模块 `spring-r2dbc`
- 支持 Java 15

#### Spring Boot 2.3

- 支持 Java 14
- Liveness 和 Readiness 探针
- 支持 R2DBC

#### Spring Boot 2.2

- Spring 5.2
- JMX 默认关闭
- 添加 JDK 13 的支持，Java 8 和 Java11 依旧支持
- Kubernetes detection

#### Spring Boot 2.1

- Spring 5.1
- 支持 JDK 11
- 支持 Kafka streams
- 自动配置的 exclusion 优化

#### Spring Boot 2.0

- 最低要求 JDK 8
- 基于 Spring 5
- 引入响应式编程如 WebFlux
- 支持 HTTP/2
- 数据池化使用 HikariCP
- Actuator endpoint 的升级等
