---
created: 2024-06-30 18:57
modified: 2024-06-30 18:57
tags:
  - 分布式
---

OpenFeign 支持对请求和响应进行 GZIP 压缩，以此来提供通信效率。只需在配置文件中配置即可，比较简单。

```yml
server:
  port: 9003
spring:
  application:
    name: consumer
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848 # Nacos Server 地址

logging:
  level:
    com.learn.openfeign.service: DEBUG

feign:
  # 压缩配置
  compression:
    request:
      enabled: true # 开启请求 GZIP 压缩
      # 配置压缩支持的 MIME TYPE
      mime-types: text/xml,application/xml,application/json
      min-request-size: 2048  # 配置最小请求阈值长度
    response:
      enabled: true # 开启响应 GZIP 压缩
```

等价的 properties 配置

```properties
# 开启请求 GZIP 压缩
feign.compression.request.enabled=true
# 配置压缩支持的 MIME TYPE
feign.compression.request.mime-types=text/xml,application/xml,application/json
# 配置最小请求阈值长度
feign.compression.request.min-request-size=2048
# 开启响应 GZIP 压缩
feign.compression.response.enabled=true
```

> [!link] Reference
> [Spring Cloud OpenFeign](https://docs.spring.io/spring-cloud-openfeign/docs/3.1.9/reference/html/#feign-requestresponse-compression)
