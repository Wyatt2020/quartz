---
created: 2024-06-19 08:36
modified: 2024-07-04 22:32
tags: [分布式]
cssclasses:
  - embed-strict
---

### Version

![[cloud-alibaba-2024 版本控制|seamless]]

> [!question]
> - [ ] 在配置文件中配置了 *spring.cloud.nacos.config.server-addr*，启动类没有添加 *@EnableDiscoveryClient*。这种情况下，Nacos 控制台可以看到服务注册成功了。为什么会成功呢？*@EnableDiscoveryClient* 注解的作用到底是什么？

### 注册中心

#### 创建子模块 nacos-provider-9001

1. 引入 `spring-cloud-starter-alibaba-nacos-discovery` 依赖

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.study</groupId>
        <artifactId>cloud-alibaba-2024</artifactId>
        <version>1.0</version>
    </parent>

    <artifactId>nacos-provider-9001</artifactId>
    <packaging>jar</packaging>
    <name>nacos-provider-9001</name>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        
        <!-- Nacos 注册中心 -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>${spring-boot.version}</version>
                <configuration>
                    <mainClass>com.study.provider.ProviderApplication</mainClass>
                    <skip>true</skip>
                </configuration>
                <executions>
                    <execution>
                        <id>repackage</id>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

2. 配置 Nacos Server 地址

```yaml
server:  
  port: 9001
spring:  
  application:  
    name: provider # 当前服务的名称  
  cloud:  
    nacos:  
      discovery:  
        server-addr: 127.0.0.1:8848 # Nacos Server 地址
```

3. 启动类，添加 `@EnableDiscoveryClient` 开启服务注册

```java
@EnableDiscoveryClient
@SpringBootApplication
public class ProviderApplication {  
    public static void main(String[] args) {  
        SpringApplication.run(ProviderApplication.class, args);  
    }  
}
```

4. 测试接口

```java
@RestController
@RequestMapping("env")
public class EnvController {

    @Autowired
    private Environment env;

    @GetMapping("getServerPort")
    public Map<String, String> getServerPort() {
        return Map.of("serverPort", env.getProperty("server.port"));
    }
}
```

5. 启动服务后，在 Nacos 控制台可以看到服务注册成功

![[Pasted image 20240619090851.png]]

### 负载均衡

#### 创建服务 Provider 9002

复制 Provider 9001 的启动配置，修改端口为 9002

![[Pasted image 20240619091415.png]]

#### RestTemplate

##### 创建子模块 nacos-consumer-9003

1. 添加 `spring-cloud-starter-alibaba-nacos-discovery`，
   `spring-cloud-starter-loadbalancer` 依赖

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <parent>
        <groupId>com.study</groupId>
        <artifactId>cloud-alibaba-2024</artifactId>
        <version>1.0</version>
    </parent>
    
    <artifactId>nacos-consumer-9003</artifactId>
    <packaging>jar</packaging>
    <name>nacos-consumer-9003</name>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>

        <!--Spring Cloud 2020 版本以后，默认移除了对 Netflix 的依赖，loadbalancer 取缔了 Netflix 的 Ribbon
    loadbalancer 成为了 Spring Cloud 负载均衡器的唯一实现
    -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-loadbalancer</artifactId>
        </dependency>
    </dependencies>
</project>
```

2. 配置 Nacos Server 地址

```yaml
server:
  port: 9003
spring:
  application:
    name: consumer # 当前服务的名称
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848 # Nacos Server 地址
```

3. 启动类，测试接口

```java
@EnableDiscoveryClient
@SpringBootApplication
public class ConsumerApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class, args);
    }

    // 给 RestTemplate 实例添加 @LoadBalanced 注解，
    // 开启 @LoadBalanced 与 Ribbon 的集成：
    @LoadBalanced
    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }

    @RestController
    public class TestController {

        private final RestTemplate restTemplate;

        @Autowired
        public TestController(RestTemplate restTemplate) {this.restTemplate = restTemplate;}

        @GetMapping("getServerPort")
        public Map<String, String> getServerPort() {
            // 将要访问的微服务名称
            String serviceName = "http://provider";
            return restTemplate.getForObject(serviceName + "/env/getServerPort", Map.class);
        }
    }
}
```

##### 负载均衡测试

启动 Provider 9001、Provider 9002、Consumer 9003 服务。

![[Pasted image 20240619093107.png]]

测试 http://localhost:9003/getServerPort 接口，可以看到默认的负载均衡策略是*轮询*。

![[recording.gif]]

### 配置中心

#### 创建子模块 nacos-config-9004

1. pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <parent>
        <groupId>com.study</groupId>
        <artifactId>cloud-alibaba-2024</artifactId>
        <version>1.0</version>
    </parent>
    
    <artifactId>nacos-config-9004</artifactId>
    <packaging>jar</packaging>
    <name>nacos-config-9004</name>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

		<!-- 配置中心 -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
        </dependency>

        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
    </dependencies>
</project>

```

2. application.yml

```yaml
server:
  port: 9004
spring:
  application:
    name: nacos-config
  profiles:
    active: dev
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848 # Nacos 服务地址
      config:
        server-addr: 127.0.0.1:8848 # Nacos 服务地址
        file-extension: yaml # 指定 yaml 格式的配置
  config:
    import:
      - nacos:nacos-config-dev.yaml?refreshEnabled=true
```

[[#springc.config.import 引入]]

说明：之所以需要配置 `spring.application.name` ，是因为它是构成 Nacos 配置管理 `dataId` 字段的一部分。

在 Nacos Spring Cloud 中，`dataId` 的完整格式如下：

```
${prefix}-${spring.profiles.active}.${file-extension}
```

- `prefix` 默认为 `spring.application.name` 的值，也可以通过配置项 `spring.cloud.nacos.config.prefix` 来配置。
- `spring.profiles.active` 即为当前环境对应的 profile，详情可以参考 [Spring Boot文档](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-profiles.html#boot-features-profiles)。 **注意：当 `spring.profiles.active` 为空时，对应的连接符 `-` 也将不存在，dataId 的拼接格式变成 `${prefix}.${file-extension}`**
- `file-exetension` 为配置内容的数据格式，可以通过配置项 `spring.cloud.nacos.config.file-extension` 来配置。目前只支持 `properties` 和 `yaml` 类型。

3. 启动类

```java
@EnableDiscoveryClient
@SpringBootApplication
public class ConfigApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConfigApplication.class, args);
    }
}
```

4. 测试接口，通过 Spring Cloud 原生注解 `@RefreshScope` 实现配置自动更新。

```java
@RefreshScope
@RestController
public class NacosConfigController {

    @Value("${config.info}")
    private String configInfo;

    @GetMapping("/get-config-info")
    public String getConfigInfo() {
        return configInfo;
    }
}
```

#### Nacos 客户端添加配置信息

1. 创建
	![[Pasted image 20240619095158.png]]
	
2. 编辑
	![[Pasted image 20240619095406.png]]
	
3. 发布
	![[Pasted image 20240619095531.png]]

#### 测试动态刷新

启动 nacos-config-9004 服务，修改 `nacos-config-dev.yaml` 配置测试动态刷新。

![[Pasted image 20240619095924.png]]

### 进阶

#### profile 粒度的配置

在日常开发中如果遇到多套环境下的不同配置，可以通过 Spring 提供的 `${spring.profiles.active}` 这个配置项选择不同情况下的配置。

```properties
spring.profiles.active=develop
```

Nacos 上新增一个 dataId 为：nacos-config-develop.yaml 的基础配置，如下所示：

```properties
Data ID: nacos-config-develop.yaml
Group: DEFAULT_GROUP
配置格式: YAML
配置内容: current.env: develop-env
```

如果需要切换到生产环境，只需要更改 ${spring.profiles.active} 参数配置即可。如下所示：

```properties
spring.profiles.active=product
```

同时生产环境上 Nacos 需要添加对应 dataId 的基础配置。例如，在生成环境下的 Nacos 添加了 dataId 为：nacos-config-product.yaml 的配置：

```properties
Data ID: nacos-config-product.yaml
Group: DEFAULT_GROUP
配置格式: YAML
配置内容: current.env: product-env
```

> [!note]
> 此案例中我们通过 `spring.profiles.active=<profilename>` 的方式写死在配置文件中，而在真正的项目实施过程中这个变量的值是需要不同环境而有不同的值。这个时候通常的做法是通过 `-Dspring.profiles.active=<profile>` 参数指定其配置来达到环境间灵活的切换。

#### 自定义 Namespace 的配置

Namespace(命名空间) 用于进行租户粒度的配置隔离。不同的命名空间下，可以存在相同的 Group 或 Data ID 的配置。Namespace 的常用场景之一是不同环境的配置的区分隔离，例如开发测试环境和生产环境的资源（如配置、服务）隔离等。

> 在没有明确指定 ${spring.cloud.nacos.config.namespace} 配置的情况下， 默认使用的是 Nacos 中 *public* 命名空间即默认的命名空间。如果需要使用自定义的命名空间，可以通过以下配置来实现：

```properties
spring.cloud.nacos.config.namespace=YOUR_NAMESPACE_ID
```

> [!note]
> **该配置必须放在 bootstrap.properties 文件中**。此外 spring.cloud.nacos.config.namespace 的值是 namespace 对应的 id，id 值可以在 Nacos 的控制台获取。并且在添加配置时注意不要选择其他的 namespace，否则将会导致读取不到正确的配置。

示例：

![[Pasted image 20240619155115.png]]

![[Pasted image 20240619155146.png]]

application.yml
```yaml
server:
  port: 9004
spring:
  application:
    name: nacos-config
  profiles:
    active: dev
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848 # Nacos 服务地址
      config:
        namespace: 0edc89e5-b63a-403d-a855-ab4332af9db3 # 命名空间
        server-addr: 127.0.0.1:8848 # Nacos 服务地址
        file-extension: yaml # 指定yaml格式的配置
  config:
    import:
      - nacos:nacos-config-dev.yaml?refreshEnabled=true
```

#### 自定义 Group 的配置

在没有明确指定 ${spring.cloud.nacos.config.group} 配置的情况下， 默认使用的是组 *DEFAULT_GROUP* 。如果需要自定义 Group，可以通过以下配置来实现：

```properties
spring.cloud.nacos.config.group=YOUR_GROUP_NAME
```

> [!note]
> **该配置必须放在 bootstrap.properties 文件中。** 并且在添加配置时 Group 的值一定要和 spring.cloud.nacos.config.group 的配置值一致。

#### 配置的优先级

Nacos Config 目前提供了三种配置能力从 Nacos 拉取相关的配置：

- A: 通过 `spring.cloud.nacos.config.shared-dataids` 支持多个共享 Data Id 的配置
    
- B: 通过 `spring.cloud.nacos.config.ext-config[n].data-id` 的方式支持多个扩展 Data Id 的配置
    
- C: 通过内部相关规则 (应用名、应用名 + Profile ) 自动生成相关的 Data Id 配置

当三种方式共同使用时，他们的一个优先级关系是: **_A < B < C_**

#### springc.config.import 引入

> [!link] Reference
> [springc.config.import 引入规则](https://sca.aliyun.com/docs/2022/user-guide/nacos/advanced-guide/#springcconfigimport-%E5%BC%95%E5%85%A5)

这里假设有一个配置文件 bootstrap.yml，升级到 2021.0.1.0 以上的版本应该怎么配置呢？

bootstrap.yml

```yaml
spring:
  cloud:
    nacos:
      config:
        name: test.yml
        group: DEFAULT_GROUP
        server-addr: 127.0.0.1:8848
        extension-configs:
          - dataId: test01.yml
            group: group_01
          - dataId: test02.yml
            group: group_02
            refresh: false
```

**注意：上面的配置和下面的配置是等价的！**

application.yml

```yaml
spring:
  cloud:
    nacos:
      config:
        group: DEFAULT_GROUP
        server-addr: 127.0.0.1:8848
  config:
    import:
      - optional:nacos:test.yml # 监听 DEFAULT_GROUP:test.yml
      - optional:nacos:test01.yml?group=group_01 # 覆盖默认 group，监听 group_01:test01.yml
      - optional:nacos:test02.yml?group=group_02&refreshEnabled=false # 不开启动态刷新
      - nacos:test03.yml # 在拉取nacos配置异常时会快速失败，会导致 spring 容器启动失败
```

使用 spring.config.import 引入配置时的注意事项如下：

1. 如果使用 spring.config.import 就不能使用 bootstrap.yml / properties 引入配置的方式了；
2. 如果引入了 spring-cloud-starter-alibaba-nacos-config，并且使用 import 方式导入配置, 项目启动时会自动检测是否引入了 nacos 条目，如果没有 import nacos 条目，会出现如下错误：

```
The spring.config.import property is missing a nacos: entry

Action:

Add a spring.config.import=nacos: property to your configuration.
If configuration is not required add spring.config.import=optional:nacos: instead.
To disable this check, set spring.cloud.nacos.config.import-check.enabled=false.
```

可以通过手动设置 spring.cloud.nacos.config.import-check.enabled=false 关闭它，但是不建议这么做，这个功能可以帮助你检查是否引入多余依赖

假如想保留以前的使用方式 ( bootstrap 引入配置)，你只需要添加依赖 spring-cloud-starter-bootstrap 依赖，不需要修改一行代码即可完成配置方式的切换！

#### 配置项参考

[注册中心 spring.cloud.nacos.discovery](https://sca.aliyun.com/docs/2022/user-guide/nacos/advanced-guide/#%E9%85%8D%E7%BD%AE%E9%A1%B9%E5%8F%82%E8%80%83-1)
[配置中心 spring.cloud.nacos.config](https://sca.aliyun.com/docs/2022/user-guide/nacos/advanced-guide/#%E9%85%8D%E7%BD%AE%E9%A1%B9%E5%8F%82%E8%80%83)

### 参考

> [!link] Reference
> [Spring Cloud Alibaba | 用户指南 | 注册配置中心](https://sca.aliyun.com/docs/2022/user-guide/nacos/overview/)
> 
> [Nacos 融合 Spring Boot，成为注册配置中心 | Nacos 官网](https://nacos.io/docs/latest/ecology/use-nacos-with-spring-boot/)
> 
> [代码示例 · nacos-example/readme-zh.md at 2022.x](https://github.com/alibaba/spring-cloud-alibaba/blob/2022.x/spring-cloud-alibaba-examples/nacos-example/readme-zh.md)
