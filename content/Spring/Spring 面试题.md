---
created: 2022-08-11 14:53
tags:
  - Spring
  - 面试题
cssclasses:
  - embed-strict
---

## 什么是 Spring Framework ？

Spring 可以说是 Java 开发中用得最多的框架，它提供了非常多的组件，能够让你在开发 Java 应用的过程变得更加容易，弹性地支持其他软件框架，可以比作一个“排插座”，其他软件框架简单地“插上”即可结合 Spring 一起使用，给开发人员带来了非常多的便利。Spring 底层 IoC 容器的设计实现也是非常完美的，在整个 Spring 应用上下文的生命周期和 Spring Bean 的生命周期的许多阶段提供了相应的扩展点，供开发者自行扩展，使得框架非常的灵活。

Spring 主要模块：

- Spring Core： 基础,可以说 Spring 其他所有的功能都需要依赖于该类库。主要提供 IOC 依赖注入功能。
- Spring Aspects ： 该模块为与 AspectJ 的集成提供支持。
- Spring AOP ：提供了面向方面的编程实现。
- Spring JDBC ：Java 数据库连接。
- Spring JMS ：Java 消息服务。
- Spring ORM ：用于支持 Hibernate 等 ORM 工具。
- Spring Web ：为创建 Web 应用程序提供支持。
- Spring Test ：提供了对 JUnit 和 TestNG 测试的支持。

## Spring 里用到了哪些设计模式?

**单例模式**：Spring 中的 Bean 默认情况下都是单例的。

**工厂模式**：工厂模式主要是通过 `BeanFactory` 和 `ApplicationContext` 来生产 Bean 对象。

**代理模式**：最常见的 AOP 的实现方式就是通过代理来实现，Spring 主要是使用 JDK 动态代理和 CGLIB 代理。

**模板方法模式**：主要是一些对数据库操作的类用到，比如 `JdbcTemplate`、`JpaTemplate`，因为查询数据库的建立连接、执行查询、关闭连接几个过程，非常适用于模板方法。

## 谈谈自己对于 Spring IoC 和 AOP 的理解

### IoC

IoC（_Inversion of Control_） 叫做控制反转，指的是通过 Spring 来管理对象的创建、配置和生命周期，这样相当于把控制权交给了 Spring，不需要人工来管理对象之间复杂的依赖关系，我们在使用的时候只要去取就好了，这样做的好处就是解耦。

在 Spring 里面，主要提供了 `BeanFactory` 和 `ApplicationContext` 两种 IOC 容器，通过他们来实现对 Bean 的管理。它们的区别就在于，`BeanFactory`  是一个最基础的 IOC 容器，提供了依赖查找，依赖注入等基础的功能。而 `ApplicationContext` 是 `BeanFactory` 的子接口，它的作用更加的强大，比如支持 AOP、事件发布、国际化等功能。

### AOP

![[AOP.md#什么是 AOP]]

![[AOP.md#通知（Advice）]]

## @Repository、@Service、@Compent、@Controller 它们有什么区别?

这四个注解的本质都是一样的，都是将被该注解标识的对象放入 Spring 　容器当中，只是为了在使用上区分不同的应用分层：

- `@Repository`：dao 层

- `@Service`：service 层

- `@Controller`：controller 层

- `@Compent`：其他不属于以上三层的统一使用该注解

## DI 是什么？

DI（_Dependency Injection_） 就是依赖注入，其实和 IoC 大致相同，只不过是**同一个概念使用了不同的角度去阐述**。

DI 所描述的**重点是在于依赖**，我们说了 **IOC 的核心功能就是在程序运行时，动态地向某个对象注入其他的依赖对象**，而这个功能就是依靠 DI 去完成的，比如我们需要注入一个对象 A，而这个对象 A 依赖一个对象 B，那么我们就需要把这个对象 B 注入到对象 A 中，这就是依赖注入。

Spring 中有三种注入方式：

- 接口注入

- 构造器注入

- set 注入

## 动态代理和静态代理有什么区别?

**静态代理**

- 由程序员创建或由特定工具自动生成源代码，再对其编译。在程序运行前，代理类的 `.class 文件` 就已经存在了
- 静态代理通常只代理一个类
- 静态代理事先知道要代理的是什么

**动态代理**

- 在程序运行时，运用反射机制动态创建而成
- 动态代理是代理一个接口下的多个实现类
- 动态代理不知道要代理什么东西，只有在运行时才知道

## JDK 动态代理和 CGLIB 代理有什么区别？

- JDK 动态代理主要是针对类实现了某个接口，AOP 则会使用 JDK 动态代理。他基于反射的机制实现，生成一个实现同样接口的一个代理类，然后通过重写方法的方式，实现对代码的增强。

- 而如果某个类没有实现接口，AOP 则会使用 CGLIB 代理。他的底层原理是基于 asm 第三方框架，通过修改字节码生成一个子类，然后重写父类的方法，实现对代码的增强。

## Spring AOP 和 AspectJ AOP 有什么区别？

Spring AOP 基于动态代理实现，属于运行时增强。

AspectJ 则属于编译时增强，主要有 3 种方式：

1. 编译时织入：指的是增强的代码和源代码我们都有，直接使用 AspectJ 编译器编译就行了，编译之后生成一个新的类，他也会作为一个正常的 Java 类装载到 JVM。
2. 编译后织入：指的是代码已经被编译成 class 文件或者已经打成 jar 包，这时候要增强的话，就是编译后织入，比如你依赖了第三方的类库，又想对他增强的话，就可以通过这种方式。
3. 加载时织入：指的是在 JVM 加载类的时候进行织入。

总结下来的话，就是 Spring AOP 只能在运行时织入，不需要单独编译，性能相比 AspectJ 编译织入的方式慢，而 AspectJ 只支持编译前后和类加载时织入，性能更好，功能更加强大。

## Spring 的 AOP 是怎么实现的

本质是通过动态代理来实现的，主要有以下几个步骤。

1. 获取增强器，例如被 Aspect 注解修饰的类。
2. 在创建每一个 bean 时，会检查是否有增强器能应用于这个 bean，简单理解就是该 bean 是否在该增强器指定的 execution 表达式中。如果是，则将增强器作为拦截器参数，使用动态代理创建 bean 的代理对象实例。
3. 当我们调用被增强过的 bean 时，就会走到代理类中，从而可以触发增强器，本质跟拦截器类似。

## 多个 AOP 的顺序怎么定

通过 `Ordered` 和 `PriorityOrdered` 接口进行排序。`PriorityOrdered`  接口的优先级比  `Ordered`  更高，如果同时实现  `PriorityOrdered`  或  `Ordered`  接口，则再按 `order` 值排序，值越小的优先级越高。

## FactoryBean 和 BeanFactory 有什么区别？

`BeanFactory` 是 Bean 的工厂， `ApplicationContext` 的父类，IOC 容器的核心，负责生产和管理 Bean 对象。

`FactoryBean` 是 Bean，可以通过实现 `FactoryBean` 接口定制实例化 Bean 的逻辑，通过代理一个 Bean 对象，对方法前后做一些操作。

## 你们项目一般是怎么把对象交给 IOC 容器管理的？/ 一般是怎么定义 Bean 的？

比较常用的 3 种

1. 注解（`@Controller`，`@Service`，`@Repository`）
2. XML（`Mybatis`、`logback` 配置类）
3. JavaConfig（`@Configuration`：`Jackson` 配置类，`RedisTemplate` 配置类）

## Spring 是怎么解决循环依赖的？

Spring 使用三级缓存去解决循环依赖的，**核心逻辑就是把实例化和初始化的步骤分开，然后放入缓存中**，供另一个对象调用。

- 第一级缓存：用来保存实例化、初始化都完成的对象

- 第二级缓存：用来保存实例化完成，但是未初始化完成的对象

- 第三级缓存：用来保存一个对象工厂，提供一个匿名内部类，用于创建二级缓存中的对象

![](202208111733385.png)

假设 A、B 互相依赖。A 对象的创建过程：

1. 创建对象 A，实例化的时候把 A 对象工厂放入三级缓存
   ![](202208111734810.png)

2. A 注入属性时，发现依赖 B，转而去实例化 B

3. 同样创建对象 B，注入属性时发现依赖 A，依次从一级到三级缓存查询 A，从三级缓存通过对象工厂拿到 A，把 A 放入二级缓存，同时删除三级缓存中的 A，此时，B 已经实例化并且初始化完成，把 B 放入一级缓存。
   ![](202208111734713.png)

4. 接着继续创建 A，顺利从一级缓存拿到实例化且初始化完成的 B 对象，A 对象创建也完成，删除二级缓存中的 A，同时把 A 放入一级缓存

5. 最后，一级缓存中保存着实例化、初始化都完成的 A、B 对象
   ![](202208111735501.png)

> Spring 解决循环依赖有两个前提条件：
>
> 1. **不全是构造器方式**的循环依赖 (否则无法分离初始化和实例化的操作)
>
> 2. **必须是单例**(否则无法保证是同一对象)

## 为什么要三级缓存？二级不行吗？

假设只有二级缓存。如果 A 的原始对象注入给 B 的属性后，A 的原始对象进行了 AOP （循环依赖出现在属性注入阶段，AOP 在属性注入阶段之后）生成了一个代理对象，此时就会出现，对于 A 而言，它的 Bean 对象其实应该是 AOP 之后的代理对象，而 B 的 A 属性对应的并不是 AOP 之后的代理对象，这就导致 B 依赖的 A 和最终的 A 不是同一个对象。

使用三级缓存主要是为了保证不管什么时候使用的都是一个对象。三级缓存中放的是生成具体对象的匿名内部类，它可以生成代理对象，也可以生成普通的实例对象。对象工厂（ObjectFactory） 生成 A 的代理对象后，把这个未初始化的代理对象放到二级缓存。那么创建 B 对象时，就可以提前拿到 A 的代理对象了。这样就保证了 B 注入的 A 和 A 对象是同一个对象。

![](202208111737100.png)

## Spring 事务传播机制有哪些？

![[@Transactional.md#Spring 事务传播行为]]

## Spring 事务隔离级别有哪些？

![[@Transactional.md#Spring 事务隔离级别]]

## Spring 的事务隔离级别是如何做到和数据库不一致的？

Spring 的事务隔离级别本质上还是通过数据库来控制的，具体是在执行事务前先执行命令修改数据库隔离级别

## Spring 事务的实现原理

Spring 事务的底层实现主要使用的技术：`AOP`（动态代理） + `ThreadLocal` + `try/catch`

- 动态代理：基本所有要进行逻辑增强的地方都会用到动态代理，AOP 底层也是通过动态代理实现。

- `ThreadLocal`：主要用于线程间的资源隔离，以此实现不同线程可以使用不同的数据源、隔离级别等等。

- `try/catch`：最终是执行 `commit` 还是 `rollback`，是根据业务逻辑处理是否抛出异常来决定。

Spring 事务的核心逻辑伪代码如下：

```java
public void invokeWithinTransaction() {
    // 1.事务资源准备
    try {
        // 2.业务逻辑处理，也就是调用被代理的方法
    } catch (Exception e) {
        // 3.出现异常，进行回滚并将异常抛出
    } finally {
        // 现场还原：还原旧的事务信息
    }
    // 4.正常执行，进行事务的提交
    // 返回业务逻辑处理结果
}
```

## @Autowired 和 @Resource 有什么区别?

- **「@Resource 是 Java 自己的注解」**，`@Resource` 有两个属性是比较重要的，分是 `name` 和 `type`；Spring 将 `@Resource` 注解的 `name` 属性解析为 bean 的名字，而 `type` 属性则解析为 bean 的类型。如果使用 `name` 属性，则使用 byName 的自动注入策略，而使用 `type` 属性时则使用 byType 自动注入策略。如果既不指定 `name` 也不指定 `type` 属性，这时将通过反射机制使用 byName 自动注入策略。

- **「@Autowired 是 Spring 的注解」**，是 Spring 2.5 版本引入的，`@Autowired` 只根据 type 进行注入，**「不会去匹配 name」**。如果涉及到 type 无法辨别注入对象时，那需要依赖 `@Qualifier` 或 `@Primary` 注解一起来修饰。
  > [[@Autowired, @Primary, @Qualifier 的区别]]

## Spring 中如何让两个 bean 按顺序加载？

1. 使用 `@DependsOn`、`depends-on`

2. 让后加载的类依赖先加载的类

```java
@Component
public class A {
    @Autowire
    private B b;
}
```

3. 使用扩展点提前加载，例如：`BeanFactoryPostProcessor`

```java
@Component
public class TestBean implements BeanFactoryPostProcessor {
  @Override
  public void postProcessBeanFactory(ConfigurableListableBeanFactory
          configurableListableBeanFactory) throws BeansException {
      // 加载bean
      beanFactory.getBean("a");
  }
｝
```

## Spring 中的常见扩展点有哪些

1. `ApplicationContextInitializer`

`initialize` 方法，在 Spring 容器刷新前触发，也就是 `refresh` 方法前被触发。

2. `BeanFactoryPostProcessor`

`postProcessBeanFactory` 方法，在加载完 Bean 定义之后，创建 Bean 实例之前被触发，通常使用该扩展点来加载一些自己的 bean 定义。

3. `BeanPostProcessor`

`postProcessBeforeInitialization` 方法，执行 bean 的初始化方法前被触发；`postProcessAfterInitialization` 方法，执行 bean 的初始化方法后被触发。

4. `@PostConstruct`

该注解被封装在 `CommonAnnotationBeanPostProcessor` 中，具体触发时间是在 `postProcessBeforeInitialization` 方法。

5. `InitializingBean`

`afterPropertiesSet` 方法，在 bean 的属性填充之后，初始化方法（`init-method`）之前被触发，该方法的作用基本等同于 `init-method`，主要用于执行初始化相关操作。

6. `ApplicationListener`，事件监听器

`onApplicationEvent` 方法，根据事件类型触发时间不同，通常使用的 `ContextRefreshedEvent` 触发时间为上下文刷新完毕，通常用于 IoC 容器构建结束后处理一些自定义逻辑。

7. `@PreDestroy`

该注解被封装在 `DestructionAwareBeanPostProcessor` 中，具体触发时间是在 `postProcessBeforeDestruction` 方法，也就是在销毁对象之前触发。

8. `DisposableBean`
   `destroy` 方法，在 bean 的销毁阶段被触发，该方法的作用基本等同于

`destroy-method`，主用用于执行销毁相关操作。

## Spring 容器在何时创建对象？

- 单例 bean，容器启动过程中创建对象
- 非单例 bean，延迟 Bean（加了 `@Lazy`，或 `<bean lazy-init=“true”/>`），在调用 `getBean()` 的同时创建对象

## Spring IoC 容器启动流程

![](202208201956501.png)

## Spring bean 的生命周期

![[Spring Bean 的生命周期.md#版本一|seamless]]

> [!info]  参考链接
> - [面试必问的 Spring，你懂了吗？](https://blog.csdn.net/v123411739/article/details/110009966)
> - [面试题系列：Spring 夺命连环 10 问](https://zhuanlan.zhihu.com/p/368769721)
> - [对线面试官系列](https://mp.weixin.qq.com/s/raiIeawWZnu3SxCl9GQEGQ#/)
> - [Spring Bean 的生命周期源码详解](https://onestar.blog.csdn.net/article/details/114624328)
> - [Spring IoC 源码解析：Spring容器启动流程](https://blog.51cto.com/u_15651175/5551839)

