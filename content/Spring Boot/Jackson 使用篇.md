---
created: 2022-02-18 21:45
modified: 2024-07-21 21:47
tags:
  - SpringBoot
---

## 全局配置

### 方式一：yml 配置

```java
spring:
  jackson:
    #日期格式化
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: GMT+8
    #设置空如何序列化
    default-property-inclusion: non_null
    serialization:
      #格式化输出
      indent_output: true
      #忽略无法转换的对象
      fail_on_empty_beans: false
    deserialization:
      #允许对象忽略json中不存在的属性
      fail_on_unknown_properties: false
    parser:
      #允许出现特殊字符和转义符
      allow_unquoted_control_chars: true
      #允许出现单引号
      allow_single_quotes: true
```

### 方式二：重新注入 ObjectMapper

```java
@Configuration
public class JacksonConfig {
    
    // 修改 Jackson2ObjectMapperBuilder 的属性，设置时间类型序列化和反序列化的格式
    @Bean
    public Jackson2ObjectMapperBuilderCustomizer customizer() {
        return builder -> {
            builder.locale(Locale.CHINA);
            builder.timeZone(TimeZone.getTimeZone(ZoneId.systemDefault()));
            builder.simpleDateFormat("yyyy-MM-dd HH:mm:ss");

            JavaTimeModule javaTimeModule = new JavaTimeModule();
            javaTimeModule.addSerializer(LocalDateTime.class, new LocalDateTimeSerializer(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")));
            javaTimeModule.addSerializer(LocalDate.class, new LocalDateSerializer(DateTimeFormatter.ofPattern("yyyy-MM-dd")));
            javaTimeModule.addSerializer(LocalTime.class, new LocalTimeSerializer(DateTimeFormatter.ofPattern("HH:mm:ss")));
            javaTimeModule.addDeserializer(LocalDateTime.class, new LocalDateTimeDeserializer(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")));
            javaTimeModule.addDeserializer(LocalDate.class, new LocalDateDeserializer(DateTimeFormatter.ofPattern("yyyy-MM-dd")));
            javaTimeModule.addDeserializer(LocalTime.class, new LocalTimeDeserializer(DateTimeFormatter.ofPattern("HH:mm:ss")));

            builder.modules(javaTimeModule);
        };
    }

    // 修改 ObjectMapper 的属性，设置自定义的序列化配置
    @Bean
    @Primary
    @ConditionalOnMissingBean(ObjectMapper.class)
    public ObjectMapper jacksonObjectMapper(Jackson2ObjectMapperBuilder builder)
    {
        ObjectMapper objectMapper = builder.createXmlMapper(false).build();

        // 通过该方法对mapper对象进行设置，所有序列化的对象都将按改规则进行系列化
        // Include.Include.ALWAYS 默认
        // Include.NON_DEFAULT 属性为默认值不序列化
        // Include.NON_EMPTY 属性为 空（""） 或者为 NULL 都不序列化，则返回的json是没有这个字段的
        // Include.NON_NULL 属性为NULL 不序列化
        objectMapper.setSerializationInclusion(JsonInclude.Include.NON_NULL);
        // 允许出现特殊字符和转义符
        objectMapper.configure(JsonParser.Feature.ALLOW_UNQUOTED_CONTROL_CHARS, true);
        // 允许出现单引号
        objectMapper.configure(JsonParser.Feature.ALLOW_SINGLE_QUOTES, true);
        objectMapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS);
        objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);

        // 字段保留，将null值转为""
        objectMapper.getSerializerProvider().setNullValueSerializer(new JsonSerializer<Object>()
        {
           @Override
           public void serialize(Object o, JsonGenerator jsonGenerator,
                                 SerializerProvider serializerProvider)
                   throws IOException
           {
               jsonGenerator.writeString("");
           }
        });

        SimpleModule simpleModule = new SimpleModule();
        /**
         *  将Long,BigInteger序列化的时候,转化为String
         */
        simpleModule.addSerializer(Long.class, ToStringSerializer.instance);
        simpleModule.addSerializer(Long.TYPE, ToStringSerializer.instance);
        simpleModule.addSerializer(BigInteger.class, ToStringSerializer.instance);

        objectMapper.registerModule(simpleModule);

        return objectMapper;
    }
}
```

## 局部配置

### @JsonProperty

类似于 SQL 里字段的别名，用于序列化，使用注解字段属性，替代原字段属性

```java
@JsonProperty("userName")
private String name;
```

### @JsonIgnore

在序列化时忽略该字段

```java
@JsonIgnore
private Long id;
```

### @JsonIgnoreProperties

与 @JsonIgnore 类似，用于类上，注解使用的是字段别名

```java
@JsonIgnoreProperties({"id","userName","birthday"})
public class User {
    private Long id;
    @JsonProperty("userName")
    private String name;
    private Integer age;
    private Date birthday;
}
```

序列化结果

```java
{"age":23}
```

### @JsonTypeName @JsonTypeInfo

用在类上，在序列化时增加一层

```java
@JsonTypeName(value = "user")
@JsonTypeInfo(include = JsonTypeInfo.As.WRAPPER_OBJECT, use = JsonTypeInfo.Id.NAME)
public class User {
    private Long id;
    @JsonProperty("userName")
    private String name;
    private Integer age;
    private Date birthday;
}
```

序列化结果

```java
{"user":{"id":1,"age":23,"birthday":1587891781603,"userName":"tom"}}
```

### @JsonRootName

定义一个根 key

```java

@JsonRootName(value = "user")
public class User {
    private String id;
}
```

使用时必须给 ObjectMapper 对象做如下设置

```java
objectMapper.enable(SerializationFeature.WRAP_ROOT_VALUE);
objectMapper.enable(DeserializationFeature.UNWRAP_ROOT_VALUE);
```

序列化结果

```java
{"user": {"id": 1}}
```

### @JsonFormat

格式化日期格式

```java
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss:SSS", timezone = "GMT+8")
private Date birthday;
```

## 工具类

```java
@Slf4j
public class JsonUtils {

    private static ObjectMapper mapper = new ObjectMapper();

    static {
        // 对于空的对象转json的时候不抛出错误
        mapper.disable(SerializationFeature.FAIL_ON_EMPTY_BEANS);
        // 允许属性名称没有引号
        mapper.configure(JsonParser.Feature.ALLOW_UNQUOTED_FIELD_NAMES, true);
        // 允许单引号
        mapper.configure(JsonParser.Feature.ALLOW_SINGLE_QUOTES, true);
        // 设置输入时忽略在json字符串中存在但在java对象实际没有的属性
        mapper.disable(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES);
        // 设置输出时包含属性的风格
        mapper.setSerializationInclusion(JsonInclude.Include.NON_NULL);
    }


    /**
     * 序列化，将对象转化为json字符串
     *
     * @param data
     * @return
     */
    public static String toJsonString(Object data) {
        if (data == null) {
            return null;
        }

        String json = null;
        try {
            json = mapper.writeValueAsString(data);
        } catch (JsonProcessingException e) {
            log.error("[{}] toJsonString error：{{}}", data.getClass().getSimpleName(), e);
        }
        return json;
    }


    /**
     * 反序列化，将json字符串转化为对象
     *
     * @param json
     * @param clazz
     * @param <T>
     * @return
     */
    public static <T> T parse(@NonNull String json, Class<T> clazz) {
        T t = null;
        try {
            t = mapper.readValue(json, clazz);
        } catch (Exception e) {
            log.error(" parse json [{}] to class [{}] error：{{}}", json, clazz.getSimpleName(), e);
        }
        return t;
    }

}
```

> [!reference] 
> [Jackson 使用篇](https://blog.csdn.net/qidasheng2012/article/details/105771052)
> 
> [从 LocalDateTime 序列化探讨全局一致性序列化](https://juejin.cn/post/6854573211528249357)
> 
> [@JsonTypeInfo 多态类型处理](https://www.jianshu.com/p/a21f1633d79c)
