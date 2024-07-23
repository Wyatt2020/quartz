---
created: 2021-08-09 11:00
modified: 2023-08-27 23:28
tags:
  - 分布式
---

> [!link] Reference
>
> [Spring Cloud Netflix 官网](https://spring.io/projects/spring-cloud-netflix)
> 
> [Spring Cloud 学习教程目录](https://mp.weixin.qq.com/s/SWp3Q0l2HAzy4mKaAYzZdA)
> 
> [3W 字带你玩转 Spring Cloud](https://mp.weixin.qq.com/s/A4yRiLBM9JTcPV8nulln2A)
> 
> [方志明 - Spring Cloud 教程](https://www.fangzhipeng.com/spring-cloud.html)

[[Spring Cloud Netflix 环境搭建]]

### Eureka

Eureka 实现了服务注册与发现机制。在微服务架构中往往会有一个注册中心，每个微服务都会向注册中心去注册自己的地址及端口信息，注册中心维护着服务名称与服务实例的对应关系。每个微服务都会定时从注册中心获取服务列表，同时汇报自己的运行情况，这样当有的服务需要调用其他服务时，就可以从自己获取到的服务列表中获取实例地址进行调用。

- Eureka 包含两个组件：「Eureka Server」 和 「Eureka Client」
- Eureka Server 提供服务注册，各个节点启动后，会在 EurekaServer 中进行注册，这样 Eureka Server 中的服务注册表中将会储存所有可用服务节点的信息，服务节点的信息可以在界面中直观的看到
- Eureka Client 是一个 Java 客户端，用于简化 EurekaServer 的交互，客户端同时也具备一个内置的，使用「轮询负载算法的负载均衡器」。在应用启动后，将会向 EurekaServer 发送心跳 (默认周期为 30 秒) 。如果 Eureka Server 在多个心跳周期内没有接收到某个节点的心跳，EurekaServer 将会从服务注册表中把这个服务节点移除掉 (「默认周期为 90s」)
- 三大角色
	- `Eureka Server`：提供服务的注册与发现
	- `Service Provider`：服务生产方，将自身服务注册到 Eureka 中，从而使服务消费方能够找到
	- `Service Consumer`：服务消费方，从 Eureka 中获取注册服务列表，从而找到消费服务

#### Eureka 自我保护机制

默认情况下，当 eureka server 在一定时间内没有收到实例的心跳，会把该实例从注册表中删除（默认是 90 秒）。但如果短时间内丢失大量的实例心跳，便会触发自我保护机制。即某时刻某一个微服务不可用，eureka 不会立即清理，依旧会对该微服务的信息进行保存。

##### 建议

- **在开发环境关闭自我保护机制**。开发期间微服务会被频繁重启，如果触发了保护机制，则旧的服务实例没有被删除，这时请求有可能跑到旧的实例中，而该实例已经关闭了，这就导致请求错误，影响开发测试。
- **在生产环境开启自我保护机制**。防止服务由于网络波没有给 Eureka 发送心跳，这就导致 Eureka 将健康的服务注销了，即使网络恢复了，该服务也不会重新注册到 Eureka 了（只有在微服务启动的时候才会发起注册请求）

#### 总结

1. `EurekaClient` 启动时， 将自己的信息注册到 `EurekaServer` 上，`EurekaServer` 会存储 `EurekaClient` 的注册信息。
2. 当 `EurekaClient` 调用服务，本地没有注册信息的缓存时，去 `EurekaServer` 中去获取注册信息。
3. `EurekaClient` 会通过心跳的方式去和 `EurekaServer` 进行连接。 默认 30s `EurekaClient` 会发送一 次心跳请求，如果超过了 90s 还没有发送心跳信息的话，`EurekaServer` 就认为 `EurekaClient` 宕机了 ，将当前 `EurekaClient` 从注册表中移除。
4. `EurekaClient` 会每隔 30s 去 `EurekaServer` 中去更新注册表。
5. Eureka 的自我保护机制， 统计 15 分钟内，如果一个服务的心跳发送比例低于 85%， `EurekaServer` 就会开启自我保护机制
	- 不会从 `EurekaServer` 中移除长时间没有收到心跳的服务。
	- `EurekaServer` 还是可以正常提供服务的。
	- 网络比较稳定时，`EurekaServer` 才会开始将自己的信息被其他节点同步过去。

