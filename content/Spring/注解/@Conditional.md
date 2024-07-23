---
created: 2022-06-08 11:01
tags:
  - Spring/注解
---

## 作用

满足条件时，才注册 bean 

## 使用位置

可以用在方法和类上

## 案例代码

根据不同的操作环境，注册不同的 bean

### 条件类

实现 `Condition` 接口，实现 `matches` 方法

```java
public class LinuxCondition implements Condition {

    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        //获取 ioc 使用的 beanFactory
        ConfigurableListableBeanFactory beanFactory = context.getBeanFactory();
        //获取类加载器
        ClassLoader classLoader = context.getClassLoader();
        //获取当前环境信息
        Environment environment = context.getEnvironment();
        //获取 bean 定义的注册类
        BeanDefinitionRegistry registry = context.getRegistry();

        String os = environment.getProperty("os.name");
        if (os.contains("linux")) {
            return true;
        }
        return false;
    }
}

public class WindowsCondition implements Condition {
    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        String os = context.getEnvironment().getProperty("os.name");
        if (os.contains("Windows")) {
            return true;
        }
        return false;
    }
}
```

### 使用 @Conditional 注解

```java
@Configuration
public class AppConfig {

    //当操作系统是 windows 时，注册 bean
    @Conditional({WindowsCondition.class})
    @Bean("user")
    public User user01() {
        return new User("tom", "123");
    }

    //当操作系统是 linux 时，注册 bean
    @Conditional({LinuxCondition.class})
    @Bean
    public Car car() {
        return new Car();
    }
}

-----------------------------------

@Test
void contextLoads() {
    System.out.println("IOC 容器创建完成！");
    AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
    User user = (User) applicationContext.getBean("user");
    System.out.println(user);
    Car car = (Car) applicationContext.getBean("car");
    System.out.println(car);
}
```
> [!info]  参考链接
> - [@Conditional 通过条件来控制 bean 的注册](http://www.itsoku.com/article/278)

