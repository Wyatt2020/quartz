---
created: 2024-03-19 10:53
modified: 2024-03-19 10:53
tags:
  - Spring/注解
---

## 作用

将一个或多个配置类加入到当前的 Spring 容器中，使得这些配置类中的 Bean 定义可以被 Spring 容器所管理。

## 案例

下面的代码将 TaskService 和 TaskConfig 这两个类加入到 Spring 容器中，以便在其他地方可以使用它们。

```java
@Configuration  
@Import({TaskService.class, TaskConfig.class})  
public @interface EnableTaskService {  
}
```