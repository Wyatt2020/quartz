---
created: 2024-06-29 19:47
modified: 2024-06-30 10:52
tags: [分布式]
---

## 服务提供者

### 1. 引入依赖

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
	<groupId>com.alibaba.cloud</groupId>
	<artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

### 2. 配置 Nacos 注册中心地址

```yml
server:
  port: 9001
  servlet:
    context-path: /provider
spring:
  application:
    name: provider # 当前服务的名称
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848 # Nacos Server 地址
```

### 3. 启动类、测试类

```java
@EnableDiscoveryClient
@SpringBootApplication
public class ProviderApplication {

    public static void main(String[] args) {
        SpringApplication.run(ProviderApplication.class, args);
    }

    @RestController
    @RequestMapping("env")
    public class EnvController {

        @Autowired
        private Environment env;

        @GetMapping("getServerPort")
        public Map<String, String> getServerPort(@RequestParam("id") String id) {
            return Map.of("serverPort", env.getProperty("server.port"), "id", id);
        }
    }
}
```

## 服务消费者

### 1. 引入依赖

openfeign
```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

loadbalancer
```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
```

> [!note] 负载均衡
> 由于 Netflix Ribbon 进入停更维护阶段，因此 Spring Cloud 2020.0.1 版本之后，删除了 Eureka 中的 Ribbon, 替代 Ribbon 的是 Spring Cloud 自带的 LoadBalancer，默认使用的是轮询的方式。
> 
> 新版本的 OpenFeign 已经移除了 Ribbon，因此我们使用 OpenFeign 时需要引入 [[LoadBalancer]] 才能调起服务。

spring web
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

nacos-discovery
```xml
<dependency>
	<groupId>com.alibaba.cloud</groupId>
	<artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

### 2. 配置 Nacos 注册中心地址

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
```

### 3. 扫描 Feign Client

#### @EnableFeignClients

作用：扫描并加载 *Feign* 客户端（被 `@FeignClient` 声明的接口）
目标：启动类

```java
@SpringBootApplication
@EnableFeignClients
@EnableDiscoveryClient
public class OpenFeignHelloApplication {
    public static void main(String[] args) {
        SpringApplication.run(OpenFeignHelloApplication.class, args);
    }
}
```

### 4. 声明 Feign Client

远程调用接口当中，一般我们称提供接口的服务为提供者，而调用接口的服务为消费者。而 OpenFeign 一定是用在消费者上。

#### @FeignClient

作用：创建 REST 客户端接口并将其作为 Spring Bean 注册。它支持使用 SC *LoadBalancer* 进行负载均衡，并允许通过相同的名称进行配置。
目标：接口
属性：
- `value` 和 `name` 属性是同义词，指定*服务提供者*应用名。
- `contextId` 属性用于指定 Bean 的名称，而不是 name 属性。
- qualifier 属性是*已废弃*的，推荐使用 `qualifiers` 属性来指定 Feign 客户端的 [[@Autowired, @Primary, @Qualifier 的区别#@Qualifier|@Qualifier]] 值。
- `url` 属性一般用于调试，可以手动指定 @FeignClient 调用的地址。
- `decode404` 属性，当发生404错误时，如果该字段为 `true`，会调用 decoder 进行解码，否则抛出 FeignException。
- `configuration` 属性用于指定自定义的 Feign 客户端配置类，可自定义 Feign 的 Encode，Decode，LogLevel，Contract。
- `fallback` 定义容错的类，当远程调用的接口失败或者超时的时候，会调用对应接口的容错逻辑，fallback 执行的类必须实现 @FeignClient 标记的接口。
- `fallbackFactory` 工厂类，用于生成 fallback 类实例，通过此属性可以实现每个接口通用的容错逻辑，以达到减少重复的代码。
- `path` 属性用于定义当前 Feign Client 的统一前缀，当*服务提供者*应用名中配置了 `server.context-path`, `server.servlet-path` 时使用。
- `primary` 属性用于指定是否将 Feign 代理标记为主要 Bean，*默认*为 `true`。

---

下面示例中：
- `HelloFeignService` 是消费者的接口。
- `provider` 是提供者的服务名称。
- `/provider/env/getServerPort` 是提供者的接口。

```java
@FeignClient(name = "provider", path = "/provider", configuration = HelloFeignServiceConfig.class)
public interface HelloFeignService {

    @GetMapping(value = "/env/getServerPort")
    String searchRepo(@RequestParam("id") String id);

}
```

HelloFeignServiceConfig

```java
@Configuration  
public class HelloFeignServiceConfig {  
  
    /**  
     * Logger.Level 的具体级别如下：  
     * NONE：不记录任何信息  
     * BASIC：仅记录请求方法、URL以及响应状态码和执行时间  
     * HEADERS：除了记录 BASIC级别的信息外，还会记录请求和响应的头信息  
     * FULL：记录所有请求与响应的明细，包括头信息、请求体、元数据  
     */  
    @Bean  
    Logger.Level feignLoggerLevel() {  
        return Logger.Level.FULL;  
    }  
}
```

### 5. Controller 接口

```java
@RestController
public class HelloFeignController {

    @Autowired
    private HelloFeignService helloFeignService;

    @GetMapping(value = "/env/getServerPort")
    public String searchGithubRepoByStr(@RequestParam("id") String id) {
        return helloFeignService.searchRepo(id);
    }
}
```

### 6. 测试接口

http://localhost:9003/env/getServerPort?id=2222

![[Pasted image 20240630181029.png]]

Debug 日志

```log
---> GET http://provider/provider/env/getServerPort?id=2222 HTTP/1.1
---> END HTTP (0-byte body)
<--- HTTP/1.1 200 (2ms)
connection: keep-alive
content-type: application/json
date: Sun, 30 Jun 2024 10:10:11 GMT
keep-alive: timeout=60
transfer-encoding: chunked
{"id":"2222","serverPort":"9001"}
<--- END HTTP (33-byte body)
```

> [!link] Reference
>  [Nacos + Spring Boot + OpenFeign 项目集成](https://mp.weixin.qq.com/s?__biz=MzkxNDQzOTcyOQ==&mid=2247488934&idx=1&sn=96924516ff843b05cdfc9373d3e4b058&cur_album_id=2812233799676723203#rd)
>  
>  [Spring Cloud 远程接口调用 OpenFeign 负载均衡实现原理详解](https://mp.weixin.qq.com/s/ybpzWMJ943RGfak0A7EMhA)
