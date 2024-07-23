---
created: 2023-06-27 16:08
tags:
  - SpringBoot
  - docker
  - 消息队列
status: doing
---

## 下载与安装

### docker-compose.yml

```yaml
version: "3.1"
services:
  rabbitmq:
    image: daocloud.io/library/rabbitmq:management
    restart: always
    container_name: rabbitmq
    ports:
      - 5672:5672
      - 15672:15672
    volumes:
      - ./data:/var/lib/rabbitmq
```

### 启动 docker 实例

```shell
docker run -d --hostname my-rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3.7.15-management
```

### 后台管理

> [!note]
>
> - 访问：http://ip:15672/
> - 默认账号：guest/guest

1. 新建管理员账号

![[clipboard 7.png]]

2. 建一个新的虚拟 host 为 `/study`

![[clipboard 8.png]]

3. 给用户配置该虚拟 host 的权限

![[clipboard 9.png]]

> [!tip]
>
> - [Windows 安装 RabbitMQ](http://www.macrozheng.com/#/architect/mall_arch_09)
> - RabbitMQ 服务端代码是使用并发式语言 erlang 编写的，所以先安装 [erlang](https://www.erlang.org/downloads)

## 原生案例

原生调用的案例，可以窥探 RabbitTemplate 封装的底层细节

### pom

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

### Producer

```java
public class Producer {
    public static final String QUEUE_NAME = "erduo";

    public void producer() throws IOException, TimeoutException {
        // 创建连接工厂
        ConnectionFactory connectionFactory = new ConnectionFactory();
        // 连接到本地server
        connectionFactory.setHost("47.108.77.227");
        // 通过连接工厂创建连接
        Connection connection = connectionFactory.newConnection();
        // 通过连接创建通道
        Channel channel = connection.createChannel();
        // 创建一个名为耳朵的队列，该队列非持久(RabbitMQ重启后会消失)、非独占(非仅用于此链接)、非自动删除(服务器将不再使用的队列删除)
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);
        String msg = "hello, 我是耳朵。" + LocalDateTime.now().toString();
        // 发布消息
        // 四个参数为：指定路由器，指定key，指定参数，和二进制数据内容
        channel.basicPublish("", QUEUE_NAME, null, msg.getBytes(StandardCharsets.UTF_8));
        System.out.println("生产者发送消息结束，发送内容为：" + msg);
        channel.close();
        connection.close();
    }

    public static void main(String[] args) throws IOException, TimeoutException {
        Producer producer = new Producer();
        producer.producer();
    }
}
```

### Consumer

```java
public class Consumer {

    public void consumer() throws IOException, TimeoutException {
        // 创建连接工厂
        ConnectionFactory connectionFactory = new ConnectionFactory();
        // 连接到本地server
        connectionFactory.setHost("47.108.77.227");
        // 通过连接工厂创建连接
        Connection connection = connectionFactory.newConnection();
        // 通过连接创建通道
        Channel channel = connection.createChannel();
        // 创建消费者，阻塞接收消息
        com.rabbitmq.client.Consumer consumer = new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("-------------------------------------------");
                System.out.println("consumerTag : " + consumerTag);
                System.out.println("exchangeName : " + envelope.getExchange());
                System.out.println("routingKey : " + envelope.getRoutingKey());
                String msg = new String(body, StandardCharsets.UTF_8);
                System.out.println("消息内容 : " + msg);

                // 消息确认
                channel.basicAck(envelope.getDeliveryTag(), false);
                System.out.println("消息已确认");
            }
        };
        // 启动消费者消费指定队列
        channel.basicConsume(Producer.QUEUE_NAME, consumer);
        //保持连接状态，消费者就能一直监听消息了
//        channel.close();
//        connection.close();
    }

    public static void main(String[] args) throws IOException, TimeoutException {
        Consumer consumer = new Consumer();
        consumer.consumer();
    }
}
```

## Spring Boot 整合 RabbitMQ

### 生产者

#### pom

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

#### application.yml

```yaml
spring:
  rabbitmq:
    host: 47.108.77.227
    port: 5672
    username: guest
    password: guest
    virtual-host: /
    # 打开消息确认机制
    publisher-confirm-type: correlated
    # 打开消息返回
    publisher-returns: true
    template:
      mandatory: true
    # 手动确认消息
    listener:
      simple:
        acknowledge-mode: manual
        prefetch: 2
```

#### 定义一个名为 erduo 的队列

```java
@Configuration
public class RabbitmqConfig {

    @Bean
    public Queue erduo() {
        // 其三个参数：durable(持久化) exclusive(独占) autoDelete(自动删除)
        // 一般只设置一下持久化即可
        return new Queue("erduo", true);
    }
}
```

#### 定义生产者

```java
@Slf4j
@Component
public class RabbitProducer {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    /**
     * 发送 byte[] 数据
     */
    public void sendByteData() {
        String message = "一条 byte[] 数据" + LocalDateTime.now().toString();
        // 指定消息类型
        MessageProperties props = MessagePropertiesBuilder.newInstance()
                .setContentType(MessageProperties.CONTENT_TYPE_TEXT_PLAIN).build();

        rabbitTemplate.send(Producer.QUEUE_NAME,new Message(message.getBytes(StandardCharsets.UTF_8), props));
        log.info("消息发送完毕");
    }

    /**
     * 发送实现了 Serializable 接口的 POJO 类
     */
    public void convertAndSend() {
        rabbitTemplate.convertAndSend(Producer.QUEUE_NAME, new User());
        log.info("消息发送完毕");
    }
}
```

### 消费者

#### @RabbitListener

- 定义在方法上，表示该方法监听指定队列，获取 `Message`
- `Message` 可以接收 `byte[]`、`String`、Java 序列化对象(实现了 `Serializable` 接口的对象)
- 如果用不到 `channel`，可以不写这个参数
- 监听多个队列：`queues = {"queues1", "queues2"}`
- <font color="#de7802">当有多个方法监听同一个队列的消息时，RabbitMQ 默认采用的是轮询的方法进行消费，因此要保证发送的 `byte[]` 数据转换成 POJO 以后，能满足所有的方法，否则会报错</font>

```java
import com.rabbitmq.client.Channel;
import org.springframework.amqp.core.Message;
import org.springframework.amqp.rabbit.annotation.RabbitListener;

@Slf4j
@Component
public class RabbitConsumer {

    /**
     * 消费所有类型的消息
     */
    @RabbitListener(queues = Producer.QUEUE_NAME)
    public void onMessage(Message message, Channel channel) throws Exception {
        log.info("Message content : " + message);
        channel.basicAck(message.getMessageProperties().getDeliveryTag(),false);
        log.info("消息已确认");
    }
}
```

#### @Payload

- 获取消息的 `body`

#### @Headers

- 获取 `MessageProperties` 的 `headers` 属性

```java
/**
 * 消费 User 类型的消息
 */
@RabbitListener(queues = Producer.QUEUE_NAME)
public void onUserMessage(@Payload User user, @Headers Map<String,Object> headers, Channel channel) throws Exception {
    log.info("Message content : {}", user);
    log.info("Message headers : {}", headers);
    channel.basicAck((long) headers.get("amqp_deliveryTag"),false);
    log.info("消息已确认");
}
```

#### @RabbitListener 配合 @RabbitHandler 消费不同类型的消息

- `@RabbitListener` 作用在类上
- `@RabbitHandler` 不能接收 `Message` 类型的消息
- <font color="#de7802">如果队列里有三种类型的消息：`byte[]`，`String` 类型和`序列化的POJO`。那么消费者必须拥有处理这三种类型消息的方法，否则消息发过来的时候就会因为无法正确转换而报错</font>

```java
import org.springframework.amqp.rabbit.annotation.RabbitHandler;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.messaging.handler.annotation.Headers;
import org.springframework.messaging.handler.annotation.Payload;

@Slf4j
@RabbitListener(queues = Producer.QUEUE_NAME)
@Component
public class RabbitConsumer {

    /**
     * 消费 String 类型的消息
     */
    @RabbitHandler
    public void onMessage(@Payload String body) throws Exception {
        log.info("Message content : " + body);
    }

    /**
     * 消费 User 类型的消息
     */
    @RabbitHandler
    public void onMessage(@Payload User user, @Headers Map<String,Object> headers, Channel channel) throws Exception {
        log.info("Message content : " + user);
        log.info("Message headers : {}", headers);
        channel.basicAck((long) headers.get("amqp_deliveryTag"),false);
        log.info("消息已确认");
    }
}
```

### 消息的序列化转换

我们知道，能被自动转换的对象只有 `byte[]`、`String`、Java 序列化对象(实现了 `Serializable` 接口的对象)，但是并不是所有的 Java 对象都会去实现 Serializable 接口，而且序列化的过程中使用的是 JDK 自带的序列化方法，效率低下。

<font color="#de7802">所以我们更普遍的做法是：使用 Jackson 先将数据转换成 JSON 格式发送给 RabbitMQ，再接收消息的时候再用 Jackson 将数据反序列化出来。</font>

默认的消息转换方案是消息转换顶层接口 `MessageConverter` 的一个子类：`SimpleMessageConverter`。除了 `SimpleMessageConverter` 之外还有一个 `Jackson2JsonMessageConverter`，我们只需要将它定义为 Bean，就可以直接使用这个转换器了。

```java
@Configuration
public class RabbitmqConfig {
    @Autowired
    private ObjectMapper jacksonObjectMapper;

    @Bean
    public MessageConverter jackson2JsonMessageConverter() {
        return new Jackson2JsonMessageConverter(jacksonObjectMapper);
    }
}
```

> [!tip]
>
> - [配置 jackson 全局一致性序列化](https://juejin.cn/post/6854573211528249357)

```java
@Configuration
public class JacksonConfig {
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

#### 定义队列

```java
@Bean
public Queue erduoJson() {
    //开启持久化
    return new Queue("erduo_json", true);
}
```

#### 生产者

```java
/**
 * 发送没有实现 Serializable 接口的 POJO 类
 */
public void sendObject() {
    rabbitTemplate.convertAndSend(RabbitJsonConsumer.JSON_QUEUE, new Client());
    log.info("消息发送完毕");
}
```

#### 消费者

```java
@Slf4j
@Component
@RabbitListener(queues = RabbitJsonConsumer.JSON_QUEUE)
public class RabbitJsonConsumer {
    public static final String JSON_QUEUE = "erduo_json";

    /**
     * 消费 Client 类型的消息
     */
    @RabbitHandler
    public void onClientMessage(Client client, @Headers Map<String,Object> headers, Channel channel) throws Exception {
        log.info("Message content : {}", client);
        log.info("Message headers : {}", headers);
        channel.basicAck((Long) headers.get(AmqpHeaders.DELIVERY_TAG),false);
        log.info("消息已确认");
    }

    /**
     * 消费 User 类型的消息
     */
    @RabbitHandler
    public void onUserMessage(User user, @Headers Map<String,Object> headers, Channel channel) throws Exception {
        log.info("Message content : {}", user);
        log.info("Message headers : {}", headers);
        channel.basicAck((Long) headers.get(AmqpHeaders.DELIVERY_TAG),false);
        log.info("消息已确认");
    }
}
```

## Exchange

![[clipboard 10.png]]

### 类型

- `fanout`：`Fanout-Exchange` 会将它接收到的消息发往所有与他绑定的 Queue 中
- `direct`：`Direct-Exchange` 会把它接收到的消息发往与它有绑定关系且 Routingkey 完全匹配的 Queue 中（**默认**）
- `topic`：`Topic-Exchange` 与 Direct-Exchange 相似，不过 Topic-Exchange 不需要全匹配，可以部分匹配，它约定：Routingkey 为一个句点号“. ”分隔的字符串（我们将被句点号“. ”分隔开的每一段独立的字符串称为一个单词）
- `header`：`Header-Exchange` 不依赖于 RoutingKey 或绑定关系来分发消息，而是根据发送的消息内容中的 headers 属性进行匹配。此模式已经不再使用

### Fanout-Exchange

![[clipboard 11.png]]

`Fanout-Exchange` 又称扇形交换机，Exchange 和 Queue 建立一个绑定关系，Exchange 会分发给所有和它有绑定关系的 Queue 中，绑定了十个 Queue 就把消息复制十份进行分发。

这种绑定关系为了效率肯定都会维护一张表，从算法效率上来说一般是 O(1)，所以 `Fanout-Exchange` 是这几个交换机中查找需要被分发队列最快的交换机。

#### 案例

新建了两个演示用的队列，然后建了一个 `Fanout-Exchange`，最后给他们都设置上绑定关系，这样一组队列和交换机的绑定设置就算完成了。

```java
@Bean
public Queue fanout1() {
    return new Queue("fanout1");
}

@Bean
public Queue fanout2() {
    return new Queue("fanout2");
}

@Bean
public FanoutExchange fanoutExchange() {
    // 三个构造参数：name durable autoDelete
    return new FanoutExchange("fanoutExchange", false, false);
}

@Bean
public Binding binding1() {
    return BindingBuilder.bind(fanout1()).to(fanoutExchange());
}

@Bean
public Binding binding2() {
    return BindingBuilder.bind(fanout2()).to(fanoutExchange());
}
```

##### 生产者

```java
/**
 * 向 fanoutExchange 交换机发送消息
 */
public void sendFanout() {
    rabbitTemplate.convertAndSend("fanoutExchange", null, new Client());
    log.info("fanoutExchange消息发送完毕。");
}
```

##### 消费者

```java
@Slf4j
@Component
public class RabbitFanoutConsumer {

    @RabbitListener(queues = "fanout1")
    public void onMessage1(Message message, Channel channel) throws IOException {
        log.info("Message content : {}", message);
        channel.basicAck(message.getMessageProperties().getDeliveryTag(), false);
        log.info("FanoutConsumer1消息已确认");
    }

    @RabbitListener(queues = "fanout2")
    public void onMessage2(Message message, Channel channel) throws IOException {
        log.info("Message content : {}", message);
        channel.basicAck(message.getMessageProperties().getDeliveryTag(), false);
        log.info("FanoutConsumer2消息已确认");
    }
}
```

### Direct-Exchange

`Direct-Exchange` 是一种精准匹配的交换机，其实默认的交换机就是 Direct 类型。

如果将 Direct 交换机都比作一所公寓的管理员，那么队列就是里面的住户(绑定关系)

管理员每天都会收到各种各样的信件(消息)，这些信件的地址不光要标明地址(ExchangeKey)还需要标明要送往哪一户(routingKey)，不然消息无法投递。

![[clipboard 12.png]]

#### 案例

向 `routingKey=sms` 的队列发送消息，`routingKey=mail` 的队列接收不到此消息

新增两个队列，绑定 directExchange 交换机，设置 direct1 的 `routingKey=sms`，设置 direct2 的 `routingKey=mail`

```java
@Bean
public Queue direct1() {
    return new Queue("direct1");
}

@Bean
public Queue direct2() {
    return new Queue("direct2");
}

@Bean
public DirectExchange directExchange() {
    return new DirectExchange("directExchange", false, false);
}

/**
 * routingKey=sms
 * @return
 */
@Bean
public Binding directBinding1() {
    return BindingBuilder.bind(direct1()).to(directExchange()).with("sms");
}

/**
 * routingKey=mail
 * @return
 */
@Bean
public Binding directBinding2() {
    return BindingBuilder.bind(direct2()).to(directExchange()).with("mail");
}
```

##### 生产者

```java
/**
 * 向 directExchange 交换机发送消息，routingKey=sms
 */
public void sendDirect() {
    rabbitTemplate.convertAndSend("directExchange", "sms", new Client());
    log.info("directExchange消息发送完毕");
}
```

##### 消费者

```java
@Slf4j
@Component
public class RabbitDirectConsumer {

    @RabbitListener(queues = "direct1")
    public void onMessageSms(Message message, Channel channel) throws IOException {
        log.info("Message Content : {}", message);
        channel.basicAck(message.getMessageProperties().getDeliveryTag(), false);
        log.info("direct1消息已确认");
    }

    @RabbitListener(queues = "direct2")
    public void onMessageMail(Message message, Channel channel) throws IOException {
        log.info("Message Content : {}", message);
        channel.basicAck(message.getMessageProperties().getDeliveryTag(), false);
        log.info("direct1消息已确认");
    }
}
```

### Topic-Exchange

`Topic-Exchange` 是直接交换机的模糊匹配版本，Topic 类型的交换器，支持使用"\*"和"#"通配符定义模糊 bindingKey，然后按照 routingKey 进行模糊匹配队列进行分发。

- `*`：能够模糊匹配一个单词（1）
- `#`：能够模糊匹配零个或多个单词（0~N）

因为加入了两个通配定义符，所以 Topic 交换机的 routingKey 也有些变化，routingKey 可以使用 `.` 将单词分开。

#### 案例

新增两个队列，绑定 topicExchange 交换机，设置 topic1 的 `routingKey=sms.*`，设置 topic2 的 `routingKey=mail.#`

```java
@Bean
public Queue topic1() {
    return new Queue("topic1");
}

@Bean
public Queue topic2() {
    return new Queue("topic2");
}

@Bean
public TopicExchange topicExchange() {
    return new TopicExchange("topicExchange", false, false);
}

/**
 * routingKey=sms.*
 * @return
 */
@Bean
public Binding topicBinding1() {
    return BindingBuilder.bind(topic1()).to(topicExchange()).with("sms.*");
}

/**
 * routingKey=mail.#
 * @return
 */
@Bean
public Binding topicBinding2() {
    return BindingBuilder.bind(topic2()).to(topicExchange()).with("mail.#");
}
```

##### 生产者

```java
/**
 * 向 directExchange 交换机发送消息
 */
public void sendTopic() {
    rabbitTemplate.convertAndSend("topicExchange", "sms", new Client()); //sms.*无法接收
    rabbitTemplate.convertAndSend("topicExchange", "sms.123", new Client());
    rabbitTemplate.convertAndSend("topicExchange", "sms.123.123", new Client()); //sms.*无法接收
    rabbitTemplate.convertAndSend("topicExchange", "mail", new Client());
    rabbitTemplate.convertAndSend("topicExchange", "mail.123", new Client());
    rabbitTemplate.convertAndSend("topicExchange", "mail.123.123", new Client());
    log.info("directExchange消息发送完毕");
}
```

##### 消费者

|        | sms | sms.123 | sms.123.123 | mail | mail.123 | mail.123.123 |
| ------ | --- | ------- | ----------- | ---- | -------- | ------------ |
| sms.\* | ×   | √       | ×           |      |          |              |
| mail.# |     |         |             | √    | √        | √            |

```java
@Slf4j
@Component
public class RabbitTopicConsumer {

    @RabbitListener(queues = "topic1")
    public void onMessageSms(Message message, Channel channel) throws IOException {
        log.info("Message Content : {}", message);
        channel.basicAck(message.getMessageProperties().getDeliveryTag(), false);
        log.info("topic1消息已确认");
    }

    @RabbitListener(queues = "topic2")
    public void onMessageMail(Message message, Channel channel) throws IOException {
        log.info("Message Content : {}", message);
        channel.basicAck(message.getMessageProperties().getDeliveryTag(), false);
        log.info("topic2消息已确认");
    }
}
```

## 进阶

### 生产者发送消息到 MQ 失败

生产者发送消息之后可能由于网络闪断等各种原因，导致我们的消息并没有发送到 MQ 之中，但是这个时候生产端又不知道消息没有发出去，这就会造成消息的丢失。

为了解决这个问题，RabbitMQ 引入了**事务机制**和**发送方确认机制（publisher confirm）**，由于事务机制过于耗费性能所以一般不用，我们着重关注**发送方确认机制**。

这个机制很好理解，就是消息发送到 MQ 那端之后，MQ 会回一个确认收到的消息给我们。

只需要在配置里面打开消息确认即可（`true` 是返回客户端，`false` 是自动删除）

```yaml
spring:
  rabbitmq:
    # 打开消息确认机制
    publisher-confirm-type: correlated
```

#### 生产者

```java
/**
 * 发送消息并接收确认信息
 */
public void sendAndConfirm() {
    //设置 RabbitTemplate 确认消息后的回调方法
    /*写法一：lambda 表达式*/
    rabbitTemplate.setConfirmCallback((correlationData, ack, cause) -> {
        log.info("CorrelationData content : {}", correlationData);
        log.info("Ack status : {}", ack);
        log.info("Cause content : {}", cause);
        if (ack) {
            log.info("消息成功发送，订单入库，更改订单状态");
        } else {
            log.info("消息发送失败：{}, 出现异常：{}", correlationData, cause);
        }
    });

    /*写法二：匿名内部类*/
    /*rabbitTemplate.setConfirmCallback(new RabbitTemplate.ConfirmCallback(){
        @Override
        public void confirm(CorrelationData correlationData, boolean ack, String cause) {
            log.info("CorrelationData content : {}", correlationData);
            log.info("Ack status : {}", ack);
            log.info("Cause content : {}", cause);
            if (ack) {
                log.info("消息成功发送，订单入库，更改订单状态");
            } else {
                log.info("消息发送失败：{}, 出现异常：{}", correlationData, cause);
            }
        }
    });*/

    //消息的唯一标识
    CorrelationData correlationData = new CorrelationData(UUID.randomUUID().toString());
    rabbitTemplate.convertAndSend(Producer.QUEUE_NAME, new Client(), correlationData);
    log.info("sendAndConfirm消息发送完毕。");
}
```

生产者代码里我们看到又多了一个参数：`CorrelationData`，这个参数是用来做消息的唯一标识，同时我们打开消息确认之后需要对 `rabbitTemplate` 多设置一个 `setConfirmCallback`，参数是一个匿名类，我们消息确认成功 or 失败之后的处理就是写在这个匿名类里面。

比如一条订单消息，当消息确认到达 MQ 确认之后再行入库或者修改订单的节点状态，如果消息没有成功到达 MQ 可以进行一次记录或者将订单状态修改。

> [!tip]
> 消息确认失败不只有消息没发过去会触发，消息发过去但是找不到对应的 Exchange，也会触发。

### MQ 接收失败或者路由失败

生产者的发送消息处理好了之后，我们就可以来看看 MQ 端的处理，MQ 可能出现两个问题：

1. 消息找不到对应的 Exchange。
2. 找到了 Exchange 但是找不到对应的 Queue。

这两种情况都可以用 RabbitMQ 提供的 mandatory 参数来解决，它会设置消息投递失败的策略，有两种策略：自动删除或返回到客户端。

既然要做可靠性，自然是设置为返回到客户端。

#### 配置

```yaml
spring:
    # 打开消息确认机制
    publisher-confirm-type: correlated
    # 打开消息返回
    publisher-returns: true
    template:
      mandatory: true
```

#### 生产者

```java
/**
 * 发送消息并设置消息投递失败的策略
 */
public void sendAndReturn() {
    //设置 RabbitTemplate 消息投递失败的策略
    /*写法一：lambda 表达式*/
    rabbitTemplate.setReturnCallback((message, replyCode, replyText, exchange, routingKey) -> {
        log.info("被退回的消息为：{}", message);
        log.info("replyCode：{}", replyCode);
        log.info("replyText：{}", replyText);
        log.info("exchange：{}", exchange);
        log.info("routingKey：{}", routingKey);
    });

    /*写法二：匿名内部类*/
    /*rabbitTemplate.setReturnCallback(new RabbitTemplate.ReturnCallback() {
        @Override
        public void returnedMessage(Message message, int replyCode, String replyText, String exchange, String routingKey) {
            log.info("被退回的消息为：{}", message);
            log.info("replyCode：{}", replyCode);
            log.info("replyText：{}", replyText);
            log.info("exchange：{}", exchange);
            log.info("routingKey：{}", routingKey);
        }
    });*/

    //这里把消息发送到一个不存在的队列
    rabbitTemplate.convertAndSend("false", new User());
    log.info("消息发送完毕。");
}
```

### 使用 @Bean 配置 RabbitTemplate

```java
@Slf4j
@Configuration
public class RabbitmqConfig {
    @Autowired
    private ObjectMapper jacksonObjectMapper;

    @Bean
    public MessageConverter jackson2JsonMessageConverter() {
        return new Jackson2JsonMessageConverter(jacksonObjectMapper);
    }

    @Bean
    public RabbitTemplate createRabbitTemplate(ConnectionFactory connectionFactory) {
        RabbitTemplate rabbitTemplate = new RabbitTemplate();
        rabbitTemplate.setConnectionFactory(connectionFactory);
        //设置jackson2JsonMessageConverter作为消息转换器
        rabbitTemplate.setMessageConverter(jackson2JsonMessageConverter());
        //设置开启Mandatory,才能触发回调函数,无论消息推送结果怎么样都强制调用回调函数
        rabbitTemplate.setMandatory(true);
        //设置 RabbitTemplate 确认消息后的回调方法
        /*写法一：lambda 表达式*/
        rabbitTemplate.setConfirmCallback((correlationData, ack, cause) -> {
            log.info("CorrelationData content : {}", correlationData);
            log.info("Ack status : {}", ack);
            log.info("Cause content : {}", cause);
        });

        /*写法二：匿名内部类*/
        /*rabbitTemplate.setConfirmCallback(new RabbitTemplate.ConfirmCallback(){
            @Override
            public void confirm(CorrelationData correlationData, boolean ack, String cause) {
                log.info("CorrelationData content : {}", correlationData);
                log.info("Ack status : {}", ack);
                log.info("Cause content : {}", cause);
            }
        });*/

        //设置 RabbitTemplate 消息投递失败的策略
        /*写法一：lambda 表达式*/
        rabbitTemplate.setReturnCallback((message, replyCode, replyText, exchange, routingKey) -> {
            log.info("被退回的消息为：{}", message);
            log.info("replyCode：{}", replyCode);
            log.info("replyText：{}", replyText);
            log.info("exchange：{}", exchange);
            log.info("routingKey：{}", routingKey);
        });

        /*写法二：匿名内部类*/
        /*rabbitTemplate.setReturnCallback(new RabbitTemplate.ReturnCallback() {
            @Override
            public void returnedMessage(Message message, int replyCode, String replyText, String exchange, String routingKey) {
                log.info("被退回的消息为：{}", message);
                log.info("replyCode：{}", replyCode);
                log.info("replyText：{}", replyText);
                log.info("exchange：{}", exchange);
                log.info("routingKey：{}", routingKey);
            }
        });*/

        return rabbitTemplate;
    }
}
```

#### 配置

```yaml
spring:
  rabbitmq:
    host: 47.108.77.227
    port: 5672
    username: normal
    password: normal
    virtual-host: /study
    # 打开消息确认机制
    publisher-confirm-type: correlated
    # 打开消息返回
    publisher-returns: true
#    template:
#      mandatory: true
    # 消费者手动确认消息
    listener:
      simple:
        acknowledge-mode: manual
```

### ConfirmCallback() 与 ReturnCallback() 的触发条件

- 情况一
  - 消息推送到 sever，交换机和队列都没找到
  - 触发 ConfirmCallback
- 情况二
  - 消息推送到 server，但是在 server 里找不到交换机
  - 触发 ConfirmCallback
- 情况三
  - 消息推送到 server，找到交换机了，但是没找到队列
  - 触发 ConfirmCallback，RetrunCallback
- 情况四
  - 消息推送成功
  - 触发 ConfirmCallback

### 消费者无法正常消费

这个解决问题的方法就是消费者的消息确认。

```yaml
spring:
  rabbitmq:
    addresses: 127.0.0.1
    host: 5672
    username: guest
    password: guest
    virtual-host: /
    # 手动确认消息
    listener:
      simple:
          acknowledge-mode: manual
```

打开手动消息确认之后，无论中间是出现消费者宕机还是代码异常，只要连接断开之后这条信息还没有被消费，那么这条消息就会被重新放入队列再次被消费。

当然这也可能会出现重复消费的情况，不过在分布式系统中幂等性是一定要做的，所以一般重复消费都会被接口的幂等给拦掉。

### 消息可靠性案例

![[clipboard 13.png]]

这个例子中的消息是先入库的，然后生产者从 DB 里面拿到数据包装成消息发给 MQ，经过消费者消费之后对 DB 数据的状态进行更改，然后重新入库。

这中间有任何步骤失败，数据的状态都是没有更新的，这时通过一个定时任务不停的去刷库，找到有问题的数据将它重新扔到生产者那里进行重新投递。

### 消息队列如何限流

RabbitMQ 提供了一种 QOS(服务质量保证)功能，即在非自动确认消息的前提下，如果一定数目的消息还未被消费确认，则不进行新消息的消费。

```yaml
spring:
  rabbitmq:
    host: 47.108.77.227
    port: 5672
    username: normal
    password: normal
    virtual-host: /study
    # 消费者手动确认消息
    listener:
      simple:
        acknowledge-mode: manual
        # 如果队列中 unacked 的消息大于等于5，则不进行新的消息消费
        prefetch: 5
```

### TTL 消息/队列

- TTL 是 Time To Live 的缩写，也就是生存时间的意思
- RabbitMQ 支持消息的过期时间，在消息发送时进行指定。从消息入队列开始计算，只要超过了消息的 ttl，那么消息会自动清除
- RabbitMQ 支持队列的过期时间，在创建队列时进行指定。从消息入队列开始计算，只要超过了队列的 ttl，那么队列会自动清除所有的消息
- 如果上述两种方法同时使用，则消息的过期时间以两者 TTL 较小的那个数值为准。

#### TTL 队列

```java
@Bean
public Queue ttlQueue() {
    Map<String, Object> args = new HashMap<>();
    // 从消息入队列开始计算，只要超过了队列的 ttl，那么队列会自动清除所有的消息
    args.put("x-message-ttl", 10000);
    return new Queue("ttlQueue",false,false,false, args);
}
```

#### TTL 消息

```java
/**
     * 发送 TTL 消息
     */
    public void sendTtl() {
        /*方式一*/
        MessagePostProcessor messagePostProcessor =  (message) -> {
            //设置消息5秒后过期
            message.getMessageProperties().setExpiration("5000");
//            message.getMessageProperties().setContentEncoding("UTF-8");
            return message;
        };
        rabbitTemplate.convertAndSend("erduo", new User(), messagePostProcessor);

        /*方式二*/
        String message = "一条消息，此消息将于10秒后过期";
        //设置消息10秒后过期
        MessageProperties props = MessagePropertiesBuilder
                .newInstance()
                .setExpiration("10000")
                .build();
        rabbitTemplate.send("ttlQueue", new Message(message.getBytes(StandardCharsets.UTF_8), props));

        log.info("TTL 消息发送完毕。");
    }
```

### DLX 死信队列

DLX（Dead-Letter-Exchange）死信队列虽然叫队列，但其实指的是 Exchange，或者说指的 Exchange 和它所属的 Queue，他俩一块构成了死信队列。

当一条消息：

- 消费被拒绝（`basic.reject`/`basic.nack`）并且 `requeue=false`
- TTL 过期
- 要进入的队列达到最大长度

这三种情况，就可以判定一条消息死了，这种消息如果我们没有做处理，它就会被自动删除。

但其实我们可以在队列上加上一个参数，使当队列中发现了死亡的消息之后会将它自动转发到某个 Exchange，由指定的 Exchange 来处理这些死亡的消息。

这个处理死亡消息的 Exchange 和之前我们讲述的 Exchange 没什么区别，依然可以绑定队列然后进行消息消费。

1. 创建死信队列，并将队列绑定到 dlxExchange 交换机

```java
@Bean
public DirectExchange dlxExchange() {
    return new DirectExchange("dlxExchange", false, false);
}

@Bean
public Queue dlxQueue() {
    return new Queue("dlxQueue", false);
}

@Bean
public Binding dlxBinding() {
    return BindingBuilder.bind(dlxQueue()).to(dlxExchange()).with("dlx");
}
```

2. 在队列设置消息死亡后的去处，如果消息死亡了，就把它转发到上面定义的死信队列去

```java
@Bean
public Queue orderTtlQueue() {
    HashMap<String, Object> args = new HashMap<>();
    args.put("x-dead-letter-exchange","dlxExchange");
    args.put("x-dead-letter-routing-key","dlx"); //fanout不需要配置
    return new Queue("order.ttl.queue", false, false, false, args);
}
```

### 延时队列

RabbitMQ 的基因中没有延时队列这回事，它不能直接指定一个队列类型为延时队列，然后去延时处理，但是我们可以将 TTL， DLX 相结合，这就能组成一个延时队列。

设想一个场景，下完订单之后 15 分钟未付款我们就要将订单关闭，这就是一个很经典的演示消费的场景，如果拿 RabbitMQ 来做，我们就需要结合 TTL+DLX 了。

先把订单消息设置好 15 分钟过期时间，然后过期后队列将消息转发给我们设置好的 DLX-Exchange，DLX-Exchange 再将分发给它绑定的队列，我们的消费者再消费这个队列中的消息，就做到了延时十五分钟消费。

1. 死信队列

```java
@Bean
public DirectExchange dlxExchange() {
    return new DirectExchange("dlxExchange", false, false);
}

@Bean
public Queue dlxQueue() {
    return new Queue("dlxQueue", false);
}

@Bean
public Binding dlxBinding() {
    return BindingBuilder.bind(dlxQueue()).to(dlxExchange()).with("dlx");
}
```

2. 订单队列，将死亡的消息转发到死信队列

```java
@Bean
public Queue orderTtlQueue() {
    HashMap<String, Object> args = new HashMap<>();
    args.put("x-dead-letter-exchange","dlxExchange");
    args.put("x-dead-letter-routing-key","dlx"); //fanout不需要配置
    return new Queue("order.ttl.queue", false, false, false, args);
}
```

3. 下单业务代码

```java
/**
 * 发送订单消息到 order.ttl.queue，消息15分钟后过期
 */
public void createAnOrder() {
    MessagePostProcessor messagePostProcessor = (message) -> {
        message.getMessageProperties().setExpiration("900000");
        return message;
    };

    rabbitTemplate.convertAndSend("order.ttl.queue", new User(), messagePostProcessor);
    log.info("订单已创建，库存已锁定，消息发送完毕。");
}
```

4. 监听死信队列

```java
@Slf4j
@Component
public class RabbitDlxConsumer {

    @RabbitListener(queues = "dlxQueue")
    public void onDlxMessage(Message message, Channel channel) throws IOException {
        log.info("订单已超时，释放库存");
        log.info("Message content : {}", message);
        channel.basicAck(message.getMessageProperties().getDeliveryTag(), false);
        log.info("消息已确认");
    }
}
```

### 镜像队列



> [!info] Reference
>
> - [dockerhub 镜像](https://hub.docker.com/_/rabbitmq?tab=description)
> - [项目地址](https://gitee.com/huangyunwu/springboot-learning.git)
