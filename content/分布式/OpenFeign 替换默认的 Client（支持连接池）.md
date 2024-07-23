---
created: 2024-07-06 09:36
modified: 2024-07-06 09:57
tags: [分布式]
---

Feign 默认是使用 JDK 原生的 URLConnection 发送 HTTP 请求，没有连接池，但是对每个地址会保持一个长连接，就是利用 HTTP 的 persistence connection。

具体源码可以参考 *FeignBlockingLoadBalancerClient* 类中的 `delegate` 成员变量。

使用其他优秀的 Client 去替换，这样可以设置连接池，超时时间等对服务之间的调用调优。下面介绍使用 Http Client 和 Okhttp 替换 Feign 默认的 Client。

### 使用 Http Client 替换默认的 Client

pom.xml

```xml
<dependency>
	<groupId>io.github.openfeign</groupId>
	<artifactId>feign-httpclient</artifactId>
</dependency>
```

application.yml

```yml
feign:
  httpclient:
    enabled: true
```

关于 Http Client 的一些配置也是可以在配置文件中配置的

![[Pasted image 20240706095121.png]]

在 `org.springframework.cloud.openfeign.clientconfig.HttpClientFeignConfiguration` 中是关于 HttpClient 的配置。

很明显当没有 `CloseableHttpClient` 这个 bean 的时候，就是会由这个类来生成 Http Client 的默认配置。所以说对于 HttpClient 的自定义配置可以通过自己注入 `CloseableHttpClient`。还有 `HttpClientConnectionManager` 管理连接的 bean。其实 OpenFeign 对 HttpClient 的支持很好，因为它的一些属性可以在配置文件中配置。

### 使用 Okhttp 替换默认的 Client

其实和 Http Client 一样的配置，也是在配置文件中开启

pom.xml

```xml
<dependency>
	<groupId>io.github.openfeign</groupId>
	<artifactId>feign-okhttp</artifactId>
</dependency>
```

application.yml

```yml
feign:
  okhttp:
    enabled: true
```

这样开启之后，Client 就被替换了。同理在 `org.springframework.cloud.openfeign.clientconfig` 包下，也有一个关于 Okhttp 的配置类（`OkHttpFeignConfiguration`）。

很明显 `OkHttpClient` 是核心功能执行的类。因为 OpenFeign 中有一个类 `FeignHttpClientProperties`，有了这个类关于 HttpClient 的属性就可以在配置文件中设置了。但是 Okhttp 没有这一个类似的类，所以一般可以自己注入一个 `OkHttpClient` 去设置这些属性

```java
@Configuration
@ConditionalOnClass(Feign.class)
@AutoConfigureBefore(FeignAutoConfiguration.class)
public class OkHttpConfig {

    @Bean
    public okhttp3.OkHttpClient okHttpClient() {
        return new okhttp3.OkHttpClient.Builder()
                //设置连接超时
                .connectTimeout(60, TimeUnit.SECONDS)
                //设置读超时
                .readTimeout(60, TimeUnit.SECONDS)
                //设置写超时
                .writeTimeout(60, TimeUnit.SECONDS)
                //是否自动重连
                .retryOnConnectionFailure(true)
                .connectionPool(new ConnectionPool())
                //构建OkHttpClient对象
                .build();
    }
}
```

关于自定义 OkHttpClient 的配置，可以参考 OpenFeign 里 `OkHttpFeignConfiguration` 的配置，例如 `ConnectionPool` 这个 bean。
