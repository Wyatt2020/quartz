---
created: 2024-07-04 18:07
modified: 2024-07-04 18:07
tags:
  - 分布式
---

### 配置

3.x 以 `feign` 作为一级前缀
```yml
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
  client:
    config:
      # 需要配置的 FeignName
      consumer:
        # 连接超时时间
        connectTimeout: 5000
        # 读超时时间
        readTimeout: 5000
```

4.x 把 `feign` 前缀改成 `spring.cloud.openfeign`

```yml
spring:
  cloud:
    openfeign:
      # 压缩配置
      compression:
        request:
          enabled: true # 开启请求 GZIP 压缩
          # 配置压缩支持的 MIME TYPE
          mime-types: text/xml,application/xml,application/json
          min-request-size: 2048  # 配置最小请求阈值长度
        response:
          enabled: true # 开启响应 GZIP 压缩
      client:
        config:
          # 需要配置的 FeignName
          consumer:
            # 连接超时时间
            connectTimeout: 5000
            # 读超时时间
            readTimeout: 5000
```


