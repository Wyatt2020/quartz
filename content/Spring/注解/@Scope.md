---
created: 2022-06-08 10:59
tags:
  - Spring/注解
---

## 作用

设置 bean 的作用域

## 参数

- `prototype`：多例，懒加载，在获取 bean 时，创建实例，获取几次就创建几个实例
- `singleton`：默认单例，饿汉式，在 IOC 容器创建时，创建实例
- `request`：同一次请求创建一个实例
- `session`：同一个 session 创建一个实例

## 案例代码
```java
@Scope("prototype")
@Bean("user")
public User user01() {
    return new User("tom", "123");
}
```

> [!info]  参考链接
> - [Spring 注解之 @Scope](https://www.cnblogs.com/nemowang1996/p/11195266.html)

