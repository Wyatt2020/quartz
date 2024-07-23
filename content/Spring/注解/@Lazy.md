---
created: 2022-06-08 11:00
tags:
  - Spring/注解
---

## 懒加载

容器启动时不创建对象，第一次使用 bean 时才创建对象，并初始化

> 注意：如果一个 bean 需要懒加载，那么依赖它的 bean 也需要加上 @Lazy 才能生效

```java
@Lazy
@Bean("user")
public User user01() {
    System.out.println("user注册成功！");
    return new User("tom", "123");
}

//依赖 user
@Lazy
@Bean
public Car car(User user) {
    System.out.println(user.hashCode());
    return new Car(user);
}
```
