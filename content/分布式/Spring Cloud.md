---
created: 2024-06-29 20:03
modified: 2024-06-29 20:03
tags: [分布式]
---
> [!link] Reference
> [Spring Cloud 官网](https://spring.io/projects/spring-cloud)

[[Spring Cloud 和 Spring Boot 的版本依赖该怎么选择]]

[[Spring Cloud 2023 最新版本该如何进行组件选型]]

![[08117144382a30ff827f1a9b0c8318fb.png]]

### 引入依赖

```xml
  <dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-dependencies</artifactId>
	<version>${spring-cloud.version}</version>
	<type>pom</type>
	<scope>import</scope>
  </dependency>
```

### 继承项目
