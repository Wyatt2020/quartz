---
created: 2023-11-29 14:56
modified: 2023-11-29 14:56
tags: [Spring/注解]
---

标注一个类为 Spring 容器的 Bean

类似的注解还有：

`@Controller`：控制器层

`@Service`：服务层

`@Repository`：持久层

`@Configuration`：配置

```java
public @interface Component {  
	// 自定义 bean 名称，默认是小写的类名
    String value() default "";  
}
```
