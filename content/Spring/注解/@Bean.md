---
created: 2022-06-08 10:53
modified: 2024-03-19 10:26
tags: [Spring/注解]
---

### 作用

在容器中注册一个 *Bean*

### 案例

```java
//定义一个配置类
@Configuration
public class AppConfig {

    /**
     * 在容器中注册一个 bean, 如果 @Bean 没有指定 name, 
     * 则默认以方法名作为 bean 的名称
     * @Bean 支持指定多个别名
     */
    @Bean({"user", "user02"})
    public User user01() {
        return new User("tom", "123");
    }
    
    //支持依赖其他 bean
    @Bean
    public Car car(User user) {
        return new Car(user);
    }
}
```

```java
@Test
void contextLoads() {
    //根据注解配置类生成 applicationContext 
    AnnotationConfigApplicationContext applicationContext = 
            new AnnotationConfigApplicationContext(AppConfig.class);
    User user = (User) applicationContext.getBean("user");
    System.out.println(user);
    Car car = (Car) applicationContext.getBean("car");
    System.out.println(car);
}
```

> [!link] Reference
> [@Bean 的用法](https://www.cnblogs.com/feiyu127/p/7700090.html)
