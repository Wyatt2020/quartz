---
created: 2024-07-05 09:02
modified: 2024-07-05 18:42
tags: [分布式]
---

Sentinel 的所有规则都可以在内存态中动态地查询及修改，修改之后立即生效（服务重启后失效）。同时 Sentinel 也提供相关 API，供您来定制自己的规则策略。

### 流量控制规则 (FlowRule) 的重要属性

|Field|说明|默认值|
|---|---|---|
|resource|资源名，资源名是限流规则的作用对象||
|count|限流阈值||
|grade|限流阈值类型，QPS 或线程数模式|QPS 模式|
|limitApp|流控针对的调用来源|`default`，代表不区分调用来源|
|strategy|调用关系限流策略：直接、链路、关联|根据资源本身（直接）|
|controlBehavior|流控效果（直接拒绝 / 排队等待 / 慢启动模式），不支持按调用关系限流|直接拒绝|

同一个资源可以同时有多个限流规则。

### 通过代码定义流量控制规则

理解上面规则的定义之后，我们可以通过调用 `FlowRuleManager.loadRules()` 方法来用硬编码的方式定义流量控制规则，比如：

```java
private static void initFlowQpsRule() {
    List<FlowRule> rules = new ArrayList<>();
    FlowRule rule1 = new FlowRule();
    rule1.setResource(resource);
    // Set max qps to 20
    rule1.setCount(20);
    rule1.setGrade(RuleConstant.FLOW_GRADE_QPS);
    rule1.setLimitApp("default");
    rules.add(rule1);
    FlowRuleManager.loadRules(rules);
}
```

更多详细内容可以参考 [流量控制](https://sentinelguard.io/zh-cn/docs/flow-control.html)。

### 通过控制台添加流控规则

在添加限流规则时，点击高级选项，可以选择三种流控模式：
- **直接**：统计当前资源的请求，触发阈值时对当前资源直接限流，也是*默认*的模式
- **关联**：统计与当前资源相关的另一个资源，触发阈值时，对当前资源限流
- **链路**：统计从指定链路访问到本资源的请求，触发阈值时，对指定链路限流

![[Pasted image 20240705091526.png]]

#### 关联模式

- 关联模式
	- 统计与当前资源相关的另一个资源，触发阈值时，对当前资源限流。
- 使用场景
	- 比如用户支付时需要修改订单状态，同时用户要查询订单。查询和修改操作会争抢数据库锁，产生竞争。业务需求是有限支付和更新订单的业务，因此当修改订单业务触发阈值时，需要对查询订单业务限流。

**示例**

- 在 OrderController 有两个端点：/order/query 和 /order/update
- 配置流控规则，当 /order/update 资源被访问的 QPS 超过 5 时，对 /order/query 请求限流

![[Pasted image 20240705092537.png]]

当 `/write` 资源访问量触发阈值时，就会对 `/read` 资源限流，避免影响 `/write` 资源。

满足下面条件可以使用关联模式：
- 两个有竞争关系的资源
- 一个优先级较高，一个优先级较低

#### 链路模式

链路模式：只针对从指定链路访问到本资源的请求做统计，判断是否超过阈值。
例如有两条请求链路：
- `/test1` → `/common`
- `/test2` → `/common`

**示例**

有查询订单和创建订单业务，两者都需要查询商品。针对从查询订单进入到查询商品的请求统计，并设置限流。
步骤：
1. 在 OrderService 中添加一个 `queryGoods` 方法
2. 在 OrderController 中，改造 /order/query 端点，调用 OrderService 中的 `queryGoods` 方法
3. 在 OrderController 中添加一个 /order/save 的端点，调用 OrderService 的 `queryGoods` 方法
4. 给 `queryGoods` 设置限流规则，从 /order/query 进入 `queryGoods` 的方法限制 QPS 必须小于 2
	![[Pasted image 20240705105627.png]]

**注意**
Sentinel 默认只标记 Controller 中的方法为资源，如果要标记其它方法，需要利用 `@SentinelResource` 注解。

```java
@SentinelResource("goods")
public Map<String, String> queryGoods() {
	return Map.of("msg", "查询成功");
}
```

Sentinel 默认会将 Controller 方法做 context 整合，导致链路模式的流控失效，需要修改 `application.yml`，添加配置。
```yml
spring:
  cloud:
    sentinel:
      web-context-unify: false # 关闭 context 整合
```

**使用 [[jmeter 压力测试工具]] 测试限流效果**

![[Pasted image 20240705110333.png]]

> [!bug] 又是被自己蠢到的一天
> 脑子想测试*链路*模式，结果手点的是*关联*模式。
> 我说怎么限流不生效，还以为是 Sentinel 的坑。

**限流效果**

/order/query 被限流

![[Pasted image 20240705110442.png]]

![[Pasted image 20240705110559.png]]

/order/save 全部正常

![[Pasted image 20240705110640.png]]

![[Pasted image 20240705110658.png]]

**Sentinel 控制台**

![[Pasted image 20240705105924.png]]

#### 总结

- 直接：对当前资源限流
- 关联：高优先级资源触发阈值，对低优先级资源限流
- 链路：阈值统计时，只统计从指定资源进入当前资源的请求，是对请求来源的限流

### 流控效果

流控效果是指请求达到流控阈值时应该采取的措施，包括三种：
- **快速失败**：达到阈值后，新的请求会被立即拒绝并抛出 FlowException.异常。是默认的处理方式。
- **warm up**：预热模式，对超出阈值的请求同样是拒绝并抛出异常。但这种模式阈值会动态变化，从一个较小值逐渐增加到最大阈值。
- **排队等待**：让所有的请求按照先后次序排队执行，两个请求的间隔不能小于指定时长

#### warm up

warm up 也叫预热模式，是应对服务*冷启动*的一种方案。请求阈值初始值是 *threshold/coldFactor*，持续指定时长后，逐渐提高到 threshold 值，而 coldFactor 的默认值是 3。

![[Pasted image 20240705111608.png]]

**示例**

新增规则
![[Pasted image 20240705174926.png]]

新增计划
![[Pasted image 20240705174957.png]]

监控数据
![[Pasted image 20240705175544.png]]

#### 排队等待

当请求超过 QPS 阈值时，快速失败和 warm up 会拒绝新的请求并抛出异常。而排队等待则是让所有请求进入一个队列中，然后按照阈值允许的时间间隔依次执行。后来的请求必须等待前面执行完成，如果请求预期的等待时间超出最大时长，则会被拒绝。

例如：QPS=5，意味着每 200ms 处理一个队列中的请求；timeout=2000，意味着预期等待超过 2000ms 的请求会被拒绝并抛出异常。

![[Pasted image 20240705172139.png]]

**示例**

新增规则
![[Pasted image 20240705174848.png]]

新增计划
![[Pasted image 20240705174623.png]]

QPS 稳定在 10
![[Pasted image 20240705174520.png]]

#### 总结

- 快速失败：QPS 超过阈值时，拒绝新的请求
- warm up：QPS 超过阈值时，拒绝新的请求；QPS 阈值是逐渐提升的，可以避免*冷启动*时高并发导致服务宕机
- 排队等待：请求会进入队列，按照阈值允许的时间间隔依次执行请求；如果请求预期等待时长大于超时时间，直接拒绝

### 热点参数限流

之前的限流是统计访问某个资源的所有请求，判断是否超过 QPS 阈值。而热点参数限流是分别==统计参数值相同的请求==，判断是否超过 QPS 阈值。

![[Pasted image 20240705180124.png]]

> [!attention] 注意
> 热点参数限流对默认的 SpringMVC 资源无效，需要使用其他手段标记资源，比如 `@SentinelResource`

**示例**
给 */order/{id}* 这个资源添加热点参数限流，规则如下：
- 默认的热点参数规则是每 1 秒请求量不超过 2
- 给 102 参数设置：每 1 秒请求量不超过 4
- 给 103 参数设置：每 1 秒请求量不超过 10

接口
```java
@RequestMapping("order")
@RestController
public class OrderController {

    @SentinelResource("hot")
    @GetMapping(value = "/{id}")
    public Map<String, Long> OrderGetById(@PathVariable("id") Long id) {
        return Map.of("id", id);
    }
}
```

新增*热点规则*
![[Pasted image 20240705181452.png]]

新增计划
![[Pasted image 20240705181846.png]]

默认 QPS 是 2
![[Pasted image 20240705183622.png]]

id=102，QPS=4
![[Pasted image 20240705183705.png]]

id=103，QPS=10
![[Pasted image 20240705183734.png]]

监控数据
![[Pasted image 20240705183428.png]]

### 参考链接
- [basic-api-resource-rule | Sentinel](https://sentinelguard.io/zh-cn/docs/basic-api-resource-rule.html)
- [限流规则-bilibili](https://www.bilibili.com/video/BV1ou411a75C?p=3&vd_source=7684be089e60d522b937867a63e07bf7)
