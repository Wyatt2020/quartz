---
created: 2023-01-11 19:11
modified: 2024-06-30 23:37
tags: [SpringBoot]
---

### 官网

[GitHub - Spring Boot](https://github.com/spring-projects/spring-boot)

[Spring Boot](https://spring.io/projects/spring-boot)

[Spring Initializr](https://start.spring.io/)

### 版本日志

| Spring Boot | JDK     |
| ----------- | ------- |
| 3.1         | 17 - 20 |
| 3.0         | 17 - 19 |
| 2.6 - 2.7   | 8 - 19  |
| 2.5         | 8 - 18  |
| 2.4         | 8 - 16  |
| 2.2 - 2.3   | 8 - 15  |
| 2.1         | 8 - 12  |
| 2.0         | 8 - 9   |
| 1.5         | 6 - 8   |

> [!link] Reference
> [[Spring Boot 版本升级主要特性]]
> 
> [Spring Boot版本更新 - 3.1正式发布，你还升得动吗 - 南瓜慢说官网](https://www.pkslow.com/archives/spring-boot-version)

### 发行说明（Release Notes）

[Wiki Home](https://github.com/spring-projects/spring-boot/wiki)

#### 版本号

![[Spring Boot 学习.png|400]]

- **主版本**：有可能进行大的架构调整，各大版本之间并不一定兼容
- **次版本**：在主版本架构不变的前提下，增加了一些新的特性或变化
- **增量版本**：BUG 修复细节的完善，用来描增量版本的不一定是数字，如：`2.0.0-SNAPSHOT`

#### 发布状态

- **GA**：General Availability，正式发布的版本，官方推荐使用该版本，国外很多项目都是使用 GA 来表示正式发布版本的
- **PRE**：预览版，主要是用来内部开发人员和测试人员测试使用（不建议使用）
- **SNAPSHOT**：快照版，可以稳定使用，且该版本会一直进行小量的优化和改进 (不建议使用)
- **RC**：Release，该版本已经相当成熟了，基本上不存在导致错误的 BUG，与即将发行的正式版相差无几

> [!tip]  [维护年限](https://spring.io/projects/spring-boot#support)
> 灰色：不再维护
> 绿色：维护社区版
> 黄色：维护商业版
> 蓝色：未来版本
> ![[Spring Boot 学习-1.png]]

### 迁移指南（Migration Guides）

#### 3.0

> [!link]
[Spring Boot 3.0 Release Notes](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-3.0-Release-Notes)
> 
> [Spring Boot 3.0 Migration Guide](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-3.0-Migration-Guide)

最低版本

| Spring Boot | 2. x | 3. x |
| ----------- | ---- | ---- |
| Java        | 8    | 17   |
| Spring      | 5    | 6    |

### 继承项目

```xml
<parent>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-parent</artifactId>  
    <version>2.7.17</version>  
    <relativePath/> <!-- lookup parent from repository -->  
</parent>
```

### 引入依赖

```xml
<dependency>  
  <groupId>org.springframework.boot</groupId>  
  <artifactId>spring-boot-dependencies</artifactId>  
  <version>${spring-boot.version}</version>  
  <type>pom</type>  
  <scope>import</scope>  
</dependency>
```

> [!link] Reference
> [Spring Boot 教程](https://www.yiibai.com/spring-boot)
> 
> [Spring Boot 框架入门教程（快速学习版）](http://c.biancheng.net/spring_boot/)
