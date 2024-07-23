---
created: 2023-11-29 15:55
modified: 2023-11-29 15:55
tags:
  - SpringBoot
---

标注 Spring Boot 主启动类，本质是三个 Spring 注解的和：
1. `@SpringBootConfiguration`：`@Configuration` 的替代，一个 Spring Boot 程序只能存在一个
2. `@EnableAutoConfiguration`：启用 Spring Boot 自动配置
3. [[@ComponentScan]]：指定扫描 bean 的位置

```java
public @interface SpringBootApplication {

	// 排除特定的自动配置类，以便永远不会应用它们
	Class<?>[] exclude() default {};

	String[] excludeName() default {};

	// 设置扫描包路径
	String[] scanBasePackages() default {};

	// 作用同 scanBasePackages，类型是 Class
	Class<?>[] scanBasePackageClasses() default {};
}
```

