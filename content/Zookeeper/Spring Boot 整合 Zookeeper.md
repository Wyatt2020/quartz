---
created: 2023-06-26 11:10 
modified: 2023-08-27 23:14
tags: Zookeeper SpringBoot
related: "[[Zookeeper]]"
---

## 版本选择

curator-recipes：5.2.0

zookeeper：3.6.3

![[clipboard 1.png]]

> [!note] [关于 Zookeeper 3.4.x 的兼容模式](https://curator.apache.org/zk-compatibility-34.html#)
>
> - Zookeeper 3.4.x 版本已经是最后一个版本，curator 最新版本也移除了对 3.4.x 的支持
> - 如果你想在 Zookeeper 3.4.x 中使用 curator ，可以选择 4.2.x 版本的 curator
> - curator 4.2.x 版本和 zookeeper 3.4.x 版本会在兼容模式下运行
> - 为了使用这种模式，你必须在版本管理工具中移除对 Zookeeper 的依赖，并且重新添加对 Zookeeper 的依赖

```xml
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-recipes</artifactId>
    <version>4.2.0</version>
    <exclusions>
        <exclusion>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
       <groupId>org.apache.zookeeper</groupId>
       <artifactId>zookeeper</artifactId>
       <version>3.4.14</version>
</dependency>
```

## pom

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>2.4.1</version>
</dependency>
<!-- ZK_curator -->
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-recipes</artifactId>
    <version>${curator-recipes.version}</version>
</dependency>
```

## application.yml

### 单机

```yaml
server:
  port: 8080

spring:
  application:
    name: zkDemo

zookeeper:
  servers: 47.108.77.227:2181 #zk连接集群，多个用逗号隔开
  sessionTimeout: 60000 #会话超时时间
  connectionTimeout: 15000 #连接超时时间
  baseSleepTime: 1000 #初始重试等待时间(毫秒)
  maxRetries: 10 #重试最大次数

logging:
  level:
    root: info
```

### 集群

```yaml
server:
  port: 8080

spring:
  application:
    name: zkDemo

zookeeper:
  servers: zk1:2181,zk2:2181,zk3:2181 #zk连接集群，多个用逗号隔开
  sessionTimeout: 60000 #会话超时时间
  connectionTimeout: 15000 #连接超时时间
  baseSleepTime: 1000 #初始重试等待时间(毫秒)
  maxRetries: 10 #重试最大次数

logging:
  level:
    root: info
```

## 配置属性

```java
@Data
@Configuration
@ConfigurationProperties(prefix = "zookeeper")
public class ZooKeeperProperty {
    /**
     * zk连接集群，多个用逗号隔开
     */
    private String servers;

    /**
     * 会话超时时间
     */
    private int sessionTimeout;

    /**
     * 连接超时时间
     */
    private int connectionTimeout;

    /**
     * 初始重试等待时间(毫秒)
     */
    private int baseSleepTime;

    /**
     * 重试最大次数
     */
    private int maxRetries;
}
```

## ZK 配置类

```java
@Configuration
//@ConditionalOnProperty(prefix = "zookeeper", name = "servers")
public class ZooKeeperAutoConfiguration {

    /**
     * 初始化连接以及重试
     * @param zooKeeperProperty 配置属性
     * @return 连接
     */
    @Bean(initMethod = "start", destroyMethod = "close")
//    @ConditionalOnMissingBean
    public CuratorFramework curatorFramework(ZooKeeperProperty zooKeeperProperty) {
        RetryPolicy retryPolicy = new ExponentialBackoffRetry(zooKeeperProperty.getBaseSleepTime(), zooKeeperProperty.getMaxRetries());
        CuratorFramework client = CuratorFrameworkFactory.builder()
                .connectString(zooKeeperProperty.getServers())
                .connectionTimeoutMs(zooKeeperProperty.getConnectionTimeout())
                .sessionTimeoutMs(zooKeeperProperty.getSessionTimeout())
                .retryPolicy(retryPolicy)
                .build();
        return client;
    }
}
```

## ZooKeeperCallback

```java
public interface ZooKeeperCallback<T> {

    T callback() throws Exception;

    String getLockPath();
}
```

## ZooKeeperTemplate

## ZkTest

> [!info] Reference
>
> - [SpringBoot 2.x 整合 Zookeeper（实现 starter）](https://www.jianshu.com/p/eee1f1517cfa)
> - [Zookeeper 框架 Curator 使用](https://www.cnblogs.com/qingyunzong/p/8666288.html)
