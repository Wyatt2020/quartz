---
created: 2024-06-30 20:00
modified: 2024-06-30 20:00
tags:
  - 分布式
---

| 依赖                   | 版本       |
| -------------------- | -------- |
| JDK                  | 8        |
| Spring Cloud         | 2020.0.2 |
| Spring Cloud Gateway | 3.0.2    |

1. 新建 Spring Boot 项目

![[clipboard 72.png]]

2. 引入 gateway 依赖

![[clipboard 73.png]]

![[clipboard 74.png]]

添加负载均衡依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-loadbalancer</artifactId>
</dependency>
```

配置路由

```
- id: admin_route
  uri: lb://renren-fast
  predicates:
  - Path=/api/**
  filters:
  - RewritePath=/api/?(?<segment>.*), /renren-fast/$\{segment}
```

配置跨域请求

```java
@Configuration
public class GulimallCorsConfiguration {

    @Bean
    public CorsWebFilter corsWebFilter() {
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();

        CorsConfiguration corsConfiguration = new CorsConfiguration();

        //配置跨域
        List<String> list = new ArrayList<>();
        list.add("*");
        corsConfiguration.setAllowedHeaders(list);
        corsConfiguration.setAllowedMethods(list);
        corsConfiguration.addAllowedOriginPattern("*");
        corsConfiguration.setAllowCredentials(true);

        source.registerCorsConfiguration("/**", corsConfiguration);
        return new CorsWebFilter(source);
    }
}
```

> [!link] Reference
> [Spring Cloud Gateway](https://docs.spring.io/spring-cloud-gateway/docs/3.0.8/reference/html/)
