---
created: 2022-06-08 10:40
tags:
  - Spring/注解
---

### 作用
`@ConfigurationProperties` 可以将 `application.yml` / `application.properties` 中的属性注入到一个 bean 里（相当于 [[@Value]] 的用法）

### 用法
1. 指定配置的 `prefix`
2. 将 bean 注入到 IOC 容器（使用 `@Configuration`，[[@Component]]，或者 [[@EnableConfigurationProperties]] 都可以）
3. 类似  `max-wait-millis` 这样的短横线命名，会自动转换成驼峰命名

![[Pasted image 20240720133639.png]]

> [!link] Reference
> [注解 @ConfigurationProperties 使用方法](https://www.cnblogs.com/tian874540961/p/12146467.html)

