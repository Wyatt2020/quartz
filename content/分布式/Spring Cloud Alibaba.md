---
created: 2021-04-19 20:42
modified: 2024-07-04 23:14
tags: [分布式]
---

> [!link] Reference
> - [Github 地址](https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md)
> - **官网**
> 	- [https://spring.io/projects/spring-cloud-alibaba#overview](https://spring.io/projects/spring-cloud-alibaba#overview)
> - **英文**
> 	- [https://github.com/alibaba/spring-cloud-alibaba](https://github.com/alibaba/spring-cloud-alibaba)
> 	- [https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html](https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html)
> - **中文**
> 	- https://github.com/alibaba/spring-cloud-alibaba/blob/2023.x/README-zh.md
> 	- [Spring Cloud Alibaba](https://sca.aliyun.com/)
> - **教程**
> 	- [尚硅谷 SpringCloud 微服务笔记](https://blog.csdn.net/hancoder/article/details/109063671)
> 	- [谷粒商城笔记](https://blog.csdn.net/hancoder/article/details/106922139)
> - **最佳实践示例**
> 	- [Spring Cloud Alibaba 最佳实践示例](https://sca.aliyun.com/docs/2023/best-practice/integrated-example/)
> 	- [Spring Boot 单体应用升级 Spring Cloud 微服务最佳实践](https://sca.aliyun.com/docs/2022/best-practice/spring-boot-to-spring-cloud/) | 版本对应关系

### Spring Boot 与 Spring Cloud Alibaba 版本对应关系

> [!news]-
> 从 **2022.x** 版本开始，Spring Cloud Alibaba 版本命名方式进行了调整，未来将对应 Spring Cloud 版本，前三位为 Spring Cloud 版本，最后一位为扩展版本，比如适配 Spring Cloud 2022.0.0 版本对应的 Spring Cloud Alibaba 第一个版本为：2022.0.0.0，第个二版本为：2022.0.0.1，依此类推。
> [版本发布说明 - Spring Cloud Alibaba官网](https://sca.aliyun.com/docs/2022/overview/version-explain/)

| **Spring Boot Version** | **Spring Cloud Alibaba Version** | **Spring Cloud Version** | **JDK Version** |
| ----------------------- | -------------------------------- | ------------------------ | --------------- |
| 3.2.x                   |                                  | 2023.x                   | ≥17             |
| 3.2.4                   | 2023.0.1.0                       | 2023.0.1                 | ≥17             |
| 3.2.0                   | 2023.0.0.0-RC1                   | 2023.0.0                 | ≥17             |
| 3.0.2                   | 2022.0.0.0                       | 2022.0.0                 | ≥17             |
| 3.0.2                   | 2022.0.0.0-RC2                   | 2022.0.0                 | ≥17             |
| 3.0.0                   | 2022.0.0.0-RC1                   | 2022.0.0                 | ≥17             |
| 2.6.13                  | 2021.0.6.0                       | 2021.0.5                 | ≥8              |
| 2.6.13                  | 2021.0.5.0                       | 2021.0.5                 | ≥8              |
| 2.6.11                  | 2021.0.4.0                       | 2021.0.4                 | ≥8              |
| 2.6.3                   | 2021.0.1.0                       | 2021.0.1                 | ≥8              |
| 2.4.2                   | 2021.1                           | 2020.0.1                 | ≥8              |
| 2.3.12.RELEASE          | 2.2.10-RC1                       | Hoxton.SR12              | ≥8              |
| 2.3.12.RELEASE          | 2.2.9.RELEASE                    | Hoxton.SR12              | ≥8              |
| 2.3.12.RELEASE          | 2.2.8.RELEASE                    | Hoxton.SR12              | ≥8              |
| 2.3.12.RELEASE          | 2.2.7.RELEASE                    | Hoxton.SR12              | ≥8              |
| 2.3.2.RELEASE           | 2.2.6.RELEASE                    | Hoxton.SR9               | ≥8              |
| 2.2.5.RELEASE           | 2.2.1.RELEASE                    | Hoxton.SR3               | ≥8              |
| 2.2.X.RELEASE           | 2.2.0.RELEASE                    | Hoxton.RELEASE           | ≥8              |
| 2.1.13.RELEASE          | 2.1.4.RELEASE                    | Greenwich.SR6            | ≥8              |
| 2.1.X.RELEASE           | 2.1.2.RELEASE                    | Greenwich                | ≥8              |
| 2.0.X.RELEASE           | 2.0.4.RELEASE(停止维护，建议升级)         | Finchley                 | ≥8              |
| 1.5.X.RELEASE           | 1.5.1.RELEASE(停止维护，建议升级)         | Edgware                  | ≥7              |

### Spring Cloud Alibaba 集成的组件版本

| **Spring Cloud Alibaba Version**                          | **Sentinel Version** | **Nacos Version** | **RocketMQ Version** | **Dubbo Version** | **Seata Version** |
| --------------------------------------------------------- | -------------------- | ----------------- | -------------------- | ----------------- | ----------------- |
| 2022.0.0.0                                                | 1.8.6                | 2.2.1             | 4.9.4                | ~                 | 1.7.0             |
| 2022.0.0.0-RC2                                            | 1.8.6                | 2.2.1             | 4.9.4                | ~                 | 1.7.0-native-rc2  |
| 2021.0.5.0                                                | 1.8.6                | 2.2.0             | 4.9.4                | ~                 | 1.6.1             |
| 2.2.10-RC1                                                | 1.8.6                | 2.2.0             | 4.9.4                | ~                 | 1.6.1             |
| 2022.0.0.0-RC1                                            | 1.8.6                | 2.2.1-RC          | 4.9.4                | ~                 | 1.6.1             |
| 2.2.9.RELEASE                                             | 1.8.5                | 2.1.0             | 4.9.4                | ~                 | 1.5.2             |
| 2021.0.4.0                                                | 1.8.5                | 2.0.4             | 4.9.4                | ~                 | 1.5.2             |
| 2.2.8.RELEASE                                             | 1.8.4                | 2.1.0             | 4.9.3                | ~                 | 1.5.1             |
| 2021.0.1.0                                                | 1.8.3                | 1.4.2             | 4.9.2                | ~                 | 1.4.2             |
| 2.2.7.RELEASE                                             | 1.8.1                | 2.0.3             | 4.6.1                | 2.7.13            | 1.3.0             |
| 2.2.6.RELEASE                                             | 1.8.1                | 1.4.2             | 4.4.0                | 2.7.8             | 1.3.0             |
| 2021.1 or 2.2.5.RELEASE or 2.1.4.RELEASE or 2.0.4.RELEASE | 1.8.0                | 1.4.1             | 4.4.0                | 2.7.8             | 1.3.0             |
| 2.2.3.RELEASE or 2.1.3.RELEASE or 2.0.3.RELEASE           | 1.8.0                | 1.3.3             | 4.4.0                | 2.7.8             | 1.3.0             |
| 2.2.1.RELEASE or 2.1.2.RELEASE or 2.0.2.RELEASE           | 1.7.1                | 1.2.1             | 4.4.0                | 2.7.6             | 1.2.0             |
| 2.2.0.RELEASE                                             | 1.7.1                | 1.1.4             | 4.4.0                | 2.7.4.1           | 1.0.0             |
| 2.1.1.RELEASE or 2.0.1.RELEASE or 1.5.1.RELEASE           | 1.7.0                | 1.1.4             | 4.4.0                | 2.7.3             | 0.9.0             |
| 2.1.0.RELEASE or 2.0.0.RELEASE or 1.5.0.RELEASE           | 1.6.3                | 1.1.1             | 4.4.0                | 2.7.3             | 0.7.1             |

### 引入依赖

```xml
<dependencies>
	<dependency>
		<groupId>com.alibaba.cloud</groupId>
		<artifactId>spring-cloud-alibaba-dependencies</artifactId>
		<version>${spring-cloud.version}</version>
		<type>pom</type>
		<scope>import</scope>
	</dependency>
</dependencies>
```

### 继承项目
