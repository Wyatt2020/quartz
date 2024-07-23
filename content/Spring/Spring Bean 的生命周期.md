---
created: 2022-06-08 14:44
tags: Spring 面试题
---

## 思维导图

![](202206081445144.png)

## 版本一

![](202206081445495.png)

- **Spring Bean 元信息配置阶段**，可以通过面向资源（XML 或 Properties）、面向注解、面向 API 进行配置

- **Spring Bean 元信息解析阶段**，把配置元信息解析成 `BeanDefinition` 对象，该对象包含定义 Bean 的所有信息（`scope`（作用域），`isLazyInit`（是否延迟初始化），`isSingleton`（是否单例）），用于实例化一个 Spring Bean

- **Spring Bean 元信息注册阶段**，将 `BeanDefinition` 保存至 `BeanDefinitionRegistry` 的 `beanDefinitionMap` 集合中

- **Spring BeanDefinition 合并阶段**，定义的 Bean 可能存在层次性关系，则需要将它们进行合并，存在相同配置则覆盖父属性，最终生成一个 `RootBeanDefinition` 对象

- **Spring Bean 的实例化阶段**，加载 `Class` 对象，通过 `Class` 对象的构造器创建实例对象，构造器注入在此处会完成。Spring 提供了实例化前后两个扩展点（`InstantiationAwareBeanPostProcessor` 的 `postProcessBeforeInstantiation`、`postProcessAfterInstantiation` 方法）

- **Spring Bean 属性赋值阶段**，在 Spring 实例化后，需要对其相关属性进行赋值，注入依赖的对象。

- **Aware 接口回调阶段**，如果实现了 `xxxAware` 接口（`BeanNameAware`、`BeanFactoryAware`、`ApplicationContextAware`），通过不同类型的 Aware 接口拿到 Spring 容器的资源（`beanName`、`BeanFactory`、`ApplicationContext`）

- **Spring Bean 初始化阶段**，这里会调用 Spring Bean 配置的初始化方法，执行顺序： 1. `@PostConstruct` 标注方法 2. 实现 `InitializingBean` 接口的 `afterPropertiesSet` 方法 3. 自定义初始化方法（`init-method`）
  在初始化阶段 Spring 提供了初始化前后两个扩展点（`BeanPostProcessor` 的 `postProcessBeforeInitialization`、`postProcessAfterInitialization` 方法）

- **Spring Bean 销毁阶段**，当容器关闭或者主动销毁某个 Bean 时则进入 Spring Bean 的销毁阶段，执行顺序： 1. `@PreDestroy` 注解的销毁动作 2. 实现了 `DisposableBean` 接口的 Bean 的回调 3. `destroy-method` 自定义的销毁方法
  这里也有一个销毁前阶段，也属于 Spring 提供的一个扩展点，`@PreDestroy` 就是基于这个实现的

- **Spring 垃圾收集（GC）**

## 版本二

![](202206081446574.png)

1. Spring 启动，查找并加载需要被 Spring 管理的 Bean，进行 Bean 的实例化
2. Bean 实例化后对将 Bean 的引用和值注入到 Bean 的属性中
3. 如果 Bean 实现了 `BeanNameAware` 接口的话，Spring 将 Bean 的 Id 传递给 `setBeanName()` 方法
4. 如果 Bean 实现了 `BeanFactoryAware` 接口的话，Spring 将调用 `setBeanFactory()` 方法，将 `BeanFactory` 容器实例传入
5. 如果 Bean 实现了 `ApplicationContextAware` 接口的话，Spring 将调用 Bean 的 `setApplicationContext()` 方法，将 bean 所在应用上下文引用传入进来。
6. 如果 Bean 实现了 `BeanPostProcessor` 接口，Spring 就将调用他们的 `postProcessBeforeInitialization()` 方法。
7. 如果 Bean  实现了 `InitializingBean` 接口，Spring 将调用他们的 `afterPropertiesSet()` 方法。类似的，如果 bean 使用 `init-method` 声明了初始化方法，该方法也会被调用
8. 如果 Bean  实现了 `BeanPostProcessor` 接口，Spring 就将调用他们的 `postProcessAfterInitialization()` 方法。
9. 此时，Bean 已经准备就绪，可以被应用程序使用了。他们将一直驻留在应用上下文中，直到应用上下文被销毁。
10. 如果 Bean 实现了 `DisposableBean` 接口，Spring 将调用它的 `destory()` 接口方法，同样，如果 bean 使用了 `destory-method`  声明销毁方法，该方法也会被调用。

> [!info]  参考链接
> - [脑图地址](http://assets.processon.com/chart_image/60448d565653bb620cd99e4d.png)

