---
created: 2022-06-08 10:56
tags:
  - Spring/注解
---

### 作用

指定自定义扫描路径。
扫描被 [[@Component]] 标注的类，将它们注册为 Spring Bean。

### 参数

`excludeFilters`：要排除的 bean
`includeFilters`：要包含的 bean

### 参数值

`FilterType.ANNOTATION`：匹配注解类型
`FilterType.ASSIGNABLE_TYPE`：匹配 class 类型

### 案例代码

```java
@ComponentScan(value = "com.study", excludeFilters = {
        @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class),
        @ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE, classes = AppConfig.class)
})
```

`@ComponentScans` 可以套娃多个 `@ComponentScan`

> [!link] Reference
>
> - [深入理解 spring 注解之 @ComponentScan 注解](https://www.cnblogs.com/jpfss/p/11171655.html)
> - [Spring 注解——使用 @ComponentScan 自动扫描组件](https://www.jianshu.com/p/64aac6461d5b)
