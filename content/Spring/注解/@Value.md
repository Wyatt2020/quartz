---
created: 2022-06-08 11:06
tags:
  - Spring/注解
---

>注意，bean 必须被 Spring 管理 `@Value` 才能生效

### 用法一：给 bean 的属性注入值
```java
@Data
@Component
public class User {
    @Value("38fysd9fhihrh9")
    private String id;
    @Value("Mario")
    private String name;
    // 性别 1:男，0:女
    @Value("1")
    private int gender;
    private Date birthday = new Date();
    @Value("false")
    private boolean isStudent;
}
```

### 用法二：获取 properties 文件的值
```properties
# application.properties
server.port=8080

# application.yml
server:
    port: 8080
```
```java
@Component
public class Config{
    @Value("${server.port}")
    private String port;
}
```

