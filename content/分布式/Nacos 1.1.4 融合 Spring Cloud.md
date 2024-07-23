---
created: 2024-06-18 23:52
modified: 2024-06-18 23:52
tags:
  - 分布式
---

### 注册中心

1. pom.xml

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

> 如果项目要使用 OpenFeign，这里有个坑要注意：[No Feign Client for loadBalancing defined. Did you forget to include...](https://blog.csdn.net/gongzi_9/article/details/123373981)

2. application.yml

```yaml
spring:  
  application:  
    name: gulimall-member # 当前服务的名称  
  cloud:  
    nacos:  
      discovery:  
        server-addr: 127.0.0.1:8848 # Nacos Server 地址
```

3. 启动类

添加 `@EnableDiscoveryClient` 注解

```java
@EnableDiscoveryClient
@SpringBootApplication
public class GulimallMemberApplication {
    public static void main(String[] args) {
        SpringApplication.run(GulimallMemberApplication.class, args);
    }
}
```

4. 在控制台可以看到服务信息

![[clipboard 57.png]]

### 配置中心

> [!link] 
> [快速开始 | https://sca.aliyun.com](https://sca.aliyun.com/docs/2023/user-guide/nacos/quick-start/?spm=5176.29160081.0.0.748065cbP8SZNw)

1. pom

```xml
<dependency>
     <groupId>com.alibaba.cloud</groupId>
     <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
 </dependency>
```

> [!attention] 注意
> 如果 Spring Boot 版本是 2.4. x，Spring Cloud 版本>=2020.0.2，需要引入 spring-cloud-starter-bootstrap（[Spring Cloud 2020 bootstrap 配置文件失效](https://blog.csdn.net/qq_28336351/article/details/113730679)）
> 
> ```xml
> <dependency>
 > <groupId>org. springframework. cloud</groupId>
> <artifactId>spring-cloud-starter-bootstrap</artifactId>
> </dependency>
> ```

2. bootstrap. properties

```properties
spring.application.name=nacos-config-example
spring.cloud.nacos.config.server-addr=127.0.0.1:8848
```

3. 启动类

添加 `@EnableDiscoveryClient` 注解

```java
@EnableDiscoveryClient
@SpringBootApplication
public class GulimallMemberApplication {
    public static void main(String[] args) {
        SpringApplication.run(GulimallMemberApplication.class, args);
    }
}
```

4. 在 nacos 新增配置

![[clipboard 58.png]]

![[clipboard 59.png|500]]

启动服务，即可获取到配置中心的值，默认读取 `服务名.properties` 的配置

![[clipboard 60.png]]

5. 实时更新配置值
添加 `@RefreshScope` 注解

```java
@RestController
@RefreshScope
@RequestMapping("member")
public class MemberController {
    @Value("${user.age}")
    private String age;
    @Value("${user.desc}")
    private String desc;

    @GetMapping("/config")
    public R config() {
        return R.ok().put("age", age).put("desc", desc);
    }
```

#### 命名空间（name space）

1. 基于环境进行隔离：开发、测试、生产
2. 基于服务进行隔离：订单服务、会员服务、仓库服务（常用）

![[clipboard 61.png]]

项目中指定命名空间
将命名空间的 id 复制到 bootstrap 配置里

![[clipboard 62.png]]

`spring.cloud.nacos.config.namespace=d7cbd7b2-39ff-4b05-92ba-802d916b5115`

![[clipboard 63.png]]

配置集：所有配置的集合

![[clipboard 64.png]]

配置集 ID：`Data Id`

![[clipboard 65.png]]

配置分组：`Group`，默认 `DEFAULT_GROUP`

![[clipboard 66.png]]

`spring.cloud.nacos.config.group=DEFAULT_GROUP`

![[clipboard 67.png]]

> [!tip]
> 工作常用：每个服务创建自己的命名空间，利用分组区分环境

#### 多配置集

1. 将不同组件的配置单独写到一个文件里

![[clipboard 68.png|500]]

![[clipboard 69.png|500]]

bootstrap. properties

```properties
spring.cloud.nacos.config.extension-configs[0].data-id=datasource.yml
spring.cloud.nacos.config.extension-configs[0].group=dev
#是否开启实时刷新配置，默认 false
spring.cloud.nacos.config.extension-configs[0].refresh=true
```

![[clipboard 70.png]]

### 权重

在控制台可以配置集群服务的权重，权重越大，分配的流量越多

![[clipboard 71.png]]

元数据 + 权重可以实现灰度发布
