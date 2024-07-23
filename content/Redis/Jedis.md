---
created: 2022-05-30 09:53
tags:
  - Redis
---

## 集成 Springboot

### 依赖

**Jedis 依赖**
```xml
<!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.7.1</version>
</dependency>
```
**全部依赖**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<!-- 为什么要引入它？https://blog.csdn.net/liangjiabao5555/article/details/104062932 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
</dependency>
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>

<!-- test -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

### application.yml

```properties
server:
  port: 8080

redis:
  database: 0
  host: localhost
  port: 6379
  password: 
  max-total: 8   # 最大连接数据库连接数
  max-idle: 8     # 最大等待连接中的数量
  min-idle: 0     # 最小等待连接中的数量
  max-wait-millis: 3000  # 最大建立连接等待时间

logging:
  level:
    root: info
```

### 配置类

新增 `RedisConfig` 配置类，将 `application.yml` 与 Redis 相关的值注入到 `RedisConfig`
```java
@Data
@Configuration
@ConfigurationProperties(prefix = "redis")
public class RedisConfig {

    /**
     * 数据库序号
     */
    private String database;

    /**
     * 地址
     */
    private String host;

    /**
     * 端口
     */
    private int port;

    /**
     * 密码
     */
    private String password;

    /**
     * 最大连接数据库连接数
     */
    private int maxTotal;

    /**
     * 最大等待连接中的数量
     */
    private int maxIdle;

    /**
     * 最小等待连接中的数量
     */
    private int minIdle;

    /**
     * 最大建立连接等待时间
     */
    private int maxWaitMillis;

}
```

### 连接池

通过实现 `InitializingBean` 接口的 `afterPropertiesSet()` 进行 Jedis 连接池的初始化
```java
@Component
public class JedisPoolWrapper implements InitializingBean {

    @Autowired
    private RedisConfig redisConfig;

    /** Jedis连接池 */
    private JedisPool jedisPool = null;

    private final static Integer TIMEOUT = 2000;

    /**
     * 初始化Bean方法
     */
    @Override
    public void afterPropertiesSet() throws Exception {
        try {
            JedisPoolConfig config = new JedisPoolConfig();
            config.setMaxTotal(redisConfig.getMaxTotal());
            config.setMaxIdle(redisConfig.getMaxIdle());
            config.setMinIdle(redisConfig.getMinIdle());
            config.setMaxWaitMillis(redisConfig.getMaxWaitMillis());

            this.jedisPool = new JedisPool(config, redisConfig.getHost(), redisConfig.getPort(), TIMEOUT, redisConfig.getPassword());
        } catch (Exception e) {
            throw new Exception("Fail to initialize jedis pool");
        }
    }

    /**
     * 从资源池中获取{@link Jedis}
     *
     * @return {@link Jedis}
     */
    public Jedis getJedis() {
        return this.jedisPool.getResource();
    }
}
```

### 测试类

```java
@Slf4j
@SpringBootTest
public class JedisTest {

    @Autowired
    private JedisPoolWrapper jedisPoolWrapper;

    @Test
    public void jedisTest() {
        Jedis jedis = jedisPoolWrapper.getJedis();
        jedis.set("a", "abc");
        log.info(jedis.get("a"));
    }
}
```
> [!info]  参考链接
> - [hutool 封装的 Redis 客户端](https://www.hutool.cn/docs/#/db/NoSQL/Redis%E5%AE%A2%E6%88%B7%E7%AB%AF%E5%B0%81%E8%A3%85-RedisDS)
> - [Jedis 工具类超全](https://blog.csdn.net/weixin_42059737/article/details/104677363?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-1.highlightwordscore&spm=1001.2101.3001.4242.2)
> - [Jedis 与 RedisTemplate 操作比较](https://www.cnblogs.com/z-sir/p/13664221.html)

