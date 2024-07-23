---
created: 2022-06-08 11:09
modified: 2023-09-02 14:08
tags:
  - Spring/注解
---

### 作用

从指定路径加载配置文件
- 使用 `classpath` 前缀表示从类路径下找属性文件
- `value` 能填多个值
- `ignoreResourceNotFound = true` 表示找不到文件就忽略不报错

```java
@SpringBootApplication
@ComponentScan("com.study")
@PropertySource(value = {"classpath:jdbc.properties"}, ignoreResourceNotFound = true)
public class ProjectApplication {
    public static void main(String[] args) {
        SpringApplication.run(ProjectApplication.class, args);
    }
}
```

### 用法

与 [[@ConfigurationProperties]] 配合使用，注入其他配置文件的属性值

```java
@Component
@PropertySource(value = {"classpath:user.yml"}, factory = PropertySourceFactory.class)
@ConfigurationProperties(prefix = "user")
public class UserYml {
    private Integer id;
    private String lastName;
    private Integer age;
    private Date birthday;
    private List<String> colorList;
    private Map<String, String> cityMap;
    private Dog dog;
//……
}
```

> [!link] [Spring Boot @PropertySource 加载配置文件](https://blog.csdn.net/wangmx1993328/article/details/81005170)