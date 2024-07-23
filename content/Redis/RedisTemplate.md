---
created: 2022-05-30 09:46
tags:
  - Redis
related:
  - "[[Spring Boot]]"
---

## Spring Boot

### 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

### application.yml

```properties
spring:
  #redis配置
  redis:
    #database: 3
    host: localhost
    port: 6379
    #password: 123456
    timeout: 500
    jedis:
      pool:
        #最大空闲连接
        max-idle: 8
        #最小空闲连接
        min-idle: 0
        # 如果赋值为-1，则表示不限制；如果pool已经分配了maxActive个jedis实例，则此时pool 的状态为exhausted(耗尽)
        max-active: 8
        # 等待可用连接的最大时间，单位毫秒，默认值为-1，表示永不超时。如果超过等待时间，则直接抛出JedisConnectionException
        max-wait: -1
```

> Springboot 2.x 使用的线程池是 `lettuce`，1.x 是 `jedis`

### 编写 RedisTemplate 配置类

```java
@Configuration
public class RedisTemplateConfig {
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        // 配置连接工厂
        template.setConnectionFactory(factory);
        //使用Jackson2JsonRedisSerializer来序列化和反序列化redis的value值（默认使用JDK的序列化方式）
        Jackson2JsonRedisSerializer jacksonSeial = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        // 指定要序列化的域，field,get和set,以及修饰符范围，ANY是都有包括private和public
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        // 指定序列化输入的类型，类必须是非final修饰的，final修饰的类，比如String,Integer等会跑出异常
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jacksonSeial.setObjectMapper(om);
        // 值采用json序列化
        template.setValueSerializer(jacksonSeial);
        //使用StringRedisSerializer来序列化和反序列化redis的key值
        template.setKeySerializer(new StringRedisSerializer());
        // 设置hash key 和value序列化模式
        template.setHashKeySerializer(new StringRedisSerializer());
        template.setHashValueSerializer(jacksonSeial);
        template.afterPropertiesSet();
        return template;
    }

    @Bean
    public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory factory) {
        StringRedisTemplate stringRedisTemplate = new StringRedisTemplate();
        stringRedisTemplate.setConnectionFactory(factory);
        return stringRedisTemplate;
    }
}
```

> [!note]
RedisTemplate 有两个模板：RedisTemplate 和 StringRedisTemplate。我们不使用 RedisTemplate，RedisTemplate 提供给我们操作对象，操作对象的时候，我们通常是以 json 格式存储。但在存储的时候，RedisTemplate 会使用 Redis 默认的内部序列化器，导致我们存进里面的是乱码之类的东西。当然了，我们可以自己定义序列化（实现Serializable接口，设置serialVersionUID），但是比较麻烦。
>
>所以使用 StringRedisTemplate 模板。StringRedisTemplate 主要给我们提供字符串操作，我们可以将实体类等转成 json 字符串即可，在取出来后，也可以转成相应的对象。


```java
@Autowired
private Gson gson;
@Autowired
private RedisTemplate<String, Object> redisTemplate;
@Autowired
private StringRedisTemplate stringRedisTemplate;

@GetMapping("/redisTemplate")
public User redisTemplate() {
    redisTemplate.opsForValue().set("redisTemplate", new User());
    redisTemplate.expire("redisTemplate", 15, TimeUnit.SECONDS);
    return (User)redisTemplate.opsForValue().get("redisTemplate");
}

@GetMapping("/stringRedisTemplate")
public String stringRedisTemplate() {
    stringRedisTemplate.opsForValue().set("redisTemplate", gson.toJson(new User()));
    stringRedisTemplate.expire("redisTemplate", 15, TimeUnit.SECONDS);
    return stringRedisTemplate.opsForValue().get("redisTemplate");
}
```


