---
created: 2023-01-02 17:27
modified: 2023-11-18 14:35
tags:
  - Maven
---

### 单继承

项目继承 Spring Boot

```xml
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>2.0.7.RELEASE</version>
	<relativePath/>
</parent>
```

### 多继承

Maven 可以使用 `<scope>import</scope>` 实现多继承，另外需要指定 `<type>pom</type>`

```xml
<dependencyManagement>
	 <dependencies>
		<!--继承 springcloud alibaba 的依赖-->
		<dependency>
			<groupId>com.alibaba.cloud</groupId>
			<artifactId>spring-cloud-alibaba-dependencies</artifactId>
			<version>${springcloud.alibaba.version}</version>
			<type>pom</type>
			<scope>import</scope>
		</dependency>
	</dependencies>
</dependencyManagement>
```

> [!link]- Reference
> [Maven 中的 pom 和 import 的作用到底是什么](https://blog.csdn.net/jerry11112/article/details/120186754)
