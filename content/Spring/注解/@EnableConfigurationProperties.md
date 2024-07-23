---
created: 2022-06-08 10:38
tags:
  - Spring/注解
---

### 作用

与 [[@ConfigurationProperties]] 搭配使用，将配置类注入到 IOC 容器

### 说明

如果一个配置类只配置 `@ConfigurationProperties` 注解，而没有使用 [[@Component]]，那么在 IOC 容器中是获取不到 properties 配置文件转化的 bean。

`@EnableConfigurationProperties` 相当于把使用 `@ConfigurationProperties` 的类进行了一次注入。

> [!link] Reference
> [@EnableConfigurationProperties 注解](https://www.jianshu.com/p/7f54da1cb2eb)

