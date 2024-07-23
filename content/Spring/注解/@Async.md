---
created: 2022-06-08 10:40
tags:
  - Spring/注解
---

## 使用方法

1. 在启动类或 Controller 加注解
```java
@EnableAsync
```

2. 在需要异步执行的方法加注解
```java
@Async
```

## 配置线程池

`@Async` 默认异步配置使用的是 `SimpleAsyncTaskExecutor`，该线程池默认给一个任务创建一个线程，若系统中不断的创建线程，最终会导致系统占用内存过高，引发 OOM 。

![](202206081042309.png)

这时，我们可以定义一个线程池，异步方法将会被自动提交到线程池中执行

1. 注册自定义的 `ThreadPoolTaskExecutor`
```java
@EnableAsync
@Configuration
public class TaskPoolConfig {

    @Bean
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        //核心线程池大小
        executor.setCorePoolSize(10);
        //最大线程数
        executor.setMaxPoolSize(20);
        //队列容量
        executor.setQueueCapacity(200);
        //活跃时间
        executor.setKeepAliveSeconds(60);
        //线程名字前缀
        executor.setThreadNamePrefix("taskExecutor-");
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        return executor;
    }
}
```

2. 在 `@Async` 指定线程池
```java
@Async("taskExecutor")
```

3. 可以在 `AsyncExecutionAspectSupport#determineAsyncExecutor` 看到，线程池变成了 `ThreadPoolTaskExecutor` 类型
![](202206081044241.png)

> [!info]  参考链接
> - [Spring系列第37篇：@EnableAsync & @Async 实现方法异步调用](http://www.itsoku.com/course/5/119)
> - [Spring 中 @Async 用法总结](https://www.cnblogs.com/jpfss/p/10273129.html)
> - [Spring 使用 @Async 注解异步处理](https://www.cnblogs.com/kxm87/p/9290285.html)
> - [推荐使用自定义线程池的模式](https://mp.weixin.qq.com/s/Djkbg6DSQqCj-OojHfe-kQ)

