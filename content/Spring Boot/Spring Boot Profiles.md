---
created: 2024-07-01 11:09
modified: 2024-07-01 11:16
tags: [SpringBoot]
---

### 激活配置

#### 2.4

激活 *application-dev.yml*

```yml
spring:
  config:
    activate:
      on-profile: 
      - dev
```

#### 2.4 以前

激活 *application-dev.yml*

```yml
spring:
  profiles:
    active: dev
```

### 引入其他配置

#### 2.4

将 *application-routers.yml* 的内容，导入到当前配置。

*Import* 可以被视为在声明它们的文档下方插入的其他文档。它们遵循与常规多文档文件相同的自上而下的顺序：导入仅被导入一次，无论声明了多少次。

```yml
spring:
  config:
    import:
      - application-routers.yml
```

> [!link] Reference
> [Spring Boot 2.4 配置文件将加载机制大变化](https://developer.aliyun.com/article/775505)
> 
> [Config file processing in Spring Boot 2.4](https://spring.io/blog/2020/08/14/config-file-processing-in-spring-boot-2-4)
