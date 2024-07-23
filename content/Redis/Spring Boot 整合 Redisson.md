---
created: 2022-05-30 10:04
status: doing
tags:
  - Redis
related:
  - "[[Spring Boot]]"
---

## 版本

> spring-boot：2.4.1
> 
> redisson-spring-boot-starter：3.15.6

### [如何选择版本](https://github.com/redisson/redisson/tree/master/redisson-spring-boot-starter#spring-boot-starter)

![|350](350-6.png)

> 如果是 `redisson-spring-data-24`，那么对应的 `springboot` 版本就是 `2.4.x` 的版本

![|500](500-4.png)

## 依赖

### 方式一：使用 redisson-spring-boot-starter

`pom.xml`

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.redisson</groupId>
    <artifactId>redisson-spring-boot-starter</artifactId>
</dependency>
```

### 方式二：使用 redisson

`pom.xml`

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.redisson</groupId>
    <artifactId>redisson</artifactId>
    <version>3.15.5</version>
</dependency>
```

## 配置

### 方式一：在同一个配置里写

`application.yml`

```properties
server:
  port: 8080

spring:
  redis:
    redisson:
      config: |
        singleServerConfig:
          #password: 123456
          address: redis://127.0.0.1:6379
          database: 0
        threads: 0
        nettyThreads: 0
        codec: !<org.redisson.codec.JsonJacksonCodec> {}
        transportMode: "NIO"

logging:
  level:
    root: info
```

**threads（线程池数量）**

- 默认值: `当前处理核数量 * 2`
- 这个线程池数量被所有 RTopic 对象监听器，RRemoteService 调用者和 RExecutorService 任务共同共享。

**nettyThreads （Netty 线程池数量）**

- 默认值: `当前处理核数量 * 2`
- 这个线程池数量是在一个 Redisson 实例内，被其创建的所有分布式数据类型和服务，以及底层客户端所一同共享的线程池里保存的线程数量。

**executor（线程池）**

- 单独提供一个用来执行所有 RTopic 对象监听器，RRemoteService 调用者和 RExecutorService 任务的线程池（ExecutorService）实例。

**eventLoopGroup**

- 用于特别指定一个 EventLoopGroup，EventLoopGroup 是用来处理所有通过 Netty 与 Redis 服务之间的连接发送和接受的消息。每一个 Redisson 都会在默认情况下自己创建管理一个 EventLoopGroup 实例。因此，如果在同一个 JVM 里面可能存在多个 Redisson 实例的情况下，采取这个配置实现多个 Redisson 实例共享一个 EventLoopGroup 的目的。
- 只有 `io.netty.channel.epoll.EpollEventLoopGroup` 或 `io.netty.channel.nio.NioEventLoopGroup` 才是允许的类型。

**transportMode（传输模式）**

- 默认值：`TransportMode.NIO`
- 可选参数：
  - `TransportMode.NIO`
  - `TransportMode.EPOLL`：需要依赖里有 `netty-transport-native-epoll` 包（Linux）
  - `TransportMode.KQUEUE`：需要依赖里有 `netty-transport-native-kqueue` 包（macOS）

**lockWatchdogTimeout（监控锁的看门狗超时，单位：毫秒）**
- 默认值：`30000`
- 该参数只适用于分布式锁的加锁请求中，未明确使用 `leaseTimeout` 参数的情况。如果该看门口未使用 `lockWatchdogTimeout` 去重新调整一个分布式锁的 `lockWatchdogTimeout` 超时，那么这个锁将变为失效状态。这个参数可以用来避免由 Redisson 客户端节点宕机或其他原因造成死锁的情况。

**codec（编码）**
- 默认值: `org.redisson.codec.JsonJacksonCodec`
- 如果将 `JsonJacksonCodec` 换成 `FstCodec`，需要添加依赖。`FST fast-serialization` 是重新实现的 Java 快速对象序列化的开发包。序列化速度更快、体积更小，而且兼容 JDK 原生的序列化，要求 JDK 1.7 支持。

```xml
<dependency>
    <groupId>de.ruedigermoeller</groupId>
    <artifactId>fst</artifactId>
    <version>2.57</version>
</dependency>
```

### 方式二：独立的 redisson 配置

`application.yml`

```properties
server:
  port: 8080

spring:
  redis:
    redisson:
      file: classpath:redisson.yml
```

`redisson.yml `

```properties
singleServerConfig:
  #password: 123456
  address: redis://127.0.0.1:6379
  database: 0
threads: 0
nettyThreads: 0
codec: !<org.redisson.codec.JsonJacksonCodec> {}
transportMode: "NIO"
```

### 方式三：使用代码配置

```java
@Configuration
public class RedissonConfig {

    @Bean
    public RedissonClient redisson() throws IOException {
        // 1.创建配置
        Config config = new Config();
        // 集群模式
        // config.useClusterServers().addNodeAddress("127.0.0.1:7004", "127.0.0.1:7001");
        // 2.根据 Config 创建出 RedissonClient 示例。可以用"rediss://"来启用SSL连接
        config.useSingleServer().setAddress("redis://127.0.0.1:6379");
        return Redisson.create(config);
    }
}
```

> [!info]  参考链接
> - [Springboot 整合 Redisson(一)搭建 Redisson 环境](https://zhuanlan.zhihu.com/p/380530036)
> - [Springboot 整合 Redisson](https://blog.csdn.net/vistaed/article/details/107026758)
> - [分布式锁中的王者方案 - Redisson](https://mp.weixin.qq.com/s?__biz=MzAwMjI0ODk0NA==&mid=2451955246&idx=1&sn=5db231b88fb9e735e907873d420f26a5&chksm=8d1c27b1ba6baea7d3ef65860276140ae30a4e2bbe45179931c3f476f1fcb30d8365905dd413&scene=178&cur_album_id=1835581086177755145#rd)
> - [Redisson 官方文档](https://github.com/redisson/redisson/wiki/%E7%9B%AE%E5%BD%95)

