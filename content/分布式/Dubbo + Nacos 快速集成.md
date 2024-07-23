---
created: 2023-07-11 21:47
modified: 2024-06-30 22:56
tags: [分布式]
---

> [!link] Reference
> [项目地址](https://gitee.com/huangyunwu/dubbo-nacos)
> 
> [Dubbo 融合 Nacos 成为注册中心（官方）](https://nacos.io/zh-cn/docs/use-nacos-with-dubbo.html)
> 
> [快速开始使用 Dubbo（官方）](https://dubbo.apache.org/zh/docsv2.7/user/quick-start/)
> 
> [Spring Boot 整合 dubbo nacos](https://liuyanzhao.com/1441974348062789632.html)

### 版本选择

> [!note] 
> [dubbo - 3.0.4](https://github.com/apache/dubbo/releases/tag/dubbo-3.0.4)
> 
> [nacos - 2.0.3](https://github.com/alibaba/nacos/releases/tag/2.0.3)
> 
> spring - 5.2.8 （根据 dubbo 版本定）
> 
> [springboot - 2.3.2.RELEASE](https://github.com/spring-projects/spring-boot/releases/tag/v2.3.2.RELEASE)

#### 如何确定 Spring Boot 版本

dubbo 引用了 5.2.8 版本的 Spring，然后根据 Spring 版本反推 Spring Boot 版本

![[Dubbo + Nacos 快速集成.png]]

![[Dubbo + Nacos 快速集成-1.png]]

Windows 下单机模式运行 nacos

`startup.cmd -m standalone`

[nacos 后台](http://localhost:8848/nacos/#/serviceManagement?dataId=&group=&appName=&namespace=)

### 父模块

新建 dubbo-nacos 子模块

目录结构

![[Dubbo + Nacos 快速集成-2.png]]

pom

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.study</groupId>
    <artifactId>dubbo-nacos</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <modules>
        <module>dubbo3-nacos2-provider</module>
        <module>dubbo3-nacos2-consumer</module>
        <module>dubbo3-nacos2-provider-api</module>
    </modules>

    <properties>
        <springboot.version>2.3.2.RELEASE</springboot.version>
        <dubbo.version>3.0.4</dubbo.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
                <version>${springboot.version}</version>
            </dependency>
            <dependency>
                <groupId>org.apache.dubbo</groupId>
                <artifactId>dubbo-registry-nacos</artifactId>
                <version>${dubbo.version}</version>
            </dependency>
            <dependency>
                <groupId>org.apache.dubbo</groupId>
                <artifactId>dubbo</artifactId>
                <version>${dubbo.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
</project>
```

### 服务提供者

新建 dubbo3-nacos2-provider 子模块

目录结构

![[Dubbo + Nacos 快速集成-3.png]]

pom

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.dubbo</groupId>
    <artifactId>dubbo-registry-nacos</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.dubbo</groupId>
    <artifactId>dubbo</artifactId>
</dependency>
```

application.yml

```yaml
server:
  port: 8040

dubbo:
  # dubbo扫描包路径
  scan:
    base-packages: com.study.provider8040.service
  # 注册服务应用名字
  application:
    name: dubbo-provider
  # dubbo协议
  protocol:
    name: dubbo
    # 随机端口
    port: -1
  # 注册中心地址
  registry:
    address: nacos://127.0.0.1:8848
```

启动类

`@EnableDubbo` 激活 Dubbo 注解驱动以及外部化配置，其 `scanBasePackages` 属性扫描指定 Java 包，将所有标注 `@DubboService` 的服务接口实现类暴露为 Spring Bean，随即被导出 Dubbo 服务。

```java
@EnableDubbo
@SpringBootApplication
public class Provider8040Application {
    public static void main(String[] args) {
        SpringApplication.run(Provider8040Application.class, args);
    }
}
```

定义服务接口

```java
package com.study.provider8040.service;

/**
 * @author huangyunwu
 * @since 2021-11-5 15:53
 */
public interface HelloService {
    String sayHello(String userName);
}
```

在服务提供方实现接口

```java
package com.study.provider8040.service.impl;

import com.study.provider8040.service.DPayService;
import org.apache.dubbo.config.annotation.DubboService;
import org.apache.dubbo.rpc.RpcContext;

/**
 * @author huangyunwu
 * @since 2021-11-5 15:56
 */
@DubboService
public class HelloServiceImpl implements HelloService {
    @Override
    public String sayHello(String userName) {
        RpcContext rpcContext = RpcContext.getContext();
        return String.format("Service [port : %d] %s(\"%s\") : Hello,%s",
                rpcContext.getLocalPort(),
                rpcContext.getMethodName(),
                userName,
                userName);
    }
}
```

### API 层

新建 dubbo3-nacos2-provider-api 子模块，用来放 provider 定义的接口，供 consumer 调用。

> [!attention]
> 目录结构需要与 provider 定义的接口保持一致。

![[Dubbo + Nacos 快速集成-4.png]]

pom

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>dubbo-nacos</artifactId>
        <groupId>com.study</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>
    <packaging>jar</packaging>

    <artifactId>dubbo3-nacos2-provider-api</artifactId>

</project>
```

### 服务消费者

新建 dubbo3-nacos2-consumer 子模块

目录结构

![[Dubbo + Nacos 快速集成-5.png]]

pom

```xml
<dependency>
    <groupId>com.study</groupId>
    <artifactId>dubbo3-nacos2-provider-api</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.dubbo</groupId>
    <artifactId>dubbo-registry-nacos</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.dubbo</groupId>
    <artifactId>dubbo</artifactId>
</dependency>
```

application.yml

```yaml
server:
  port: 8050

dubbo:
  # 注册服务应用名字
  application:
    name: dubbo-consumer
  # 注册中心地址
  registry:
    address: nacos://127.0.0.1:8848
```

启动类
`@EnableDubbo` 注解激活 Dubbo 注解驱动和外部化配置，不过当前属于服务消费者，无需指定 Java 包名扫描标注 `@DubboService` 的服务实现。

```java
@EnableDubbo
@SpringBootApplication
public class Consumer8050Application {
    public static void main(String[] args) {
        SpringApplication.run(Consumer8050Application.class, args);
    }
}
```

调用远程服务

```java
package com.study.consumer8050.controller;

import com.study.provider8040.service.HelloService;
import org.apache.dubbo.config.annotation.DubboReference;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @author huangyunwu
 * @since 2021-11-5 16:12
 */
@RestController
public class HelloController {

    @DubboReference
    private HelloService helloService;

    @RequestMapping("/syaHello")
    public String syaHello() {
        return helloService.syaHello("dubbo");
    }
}
```
