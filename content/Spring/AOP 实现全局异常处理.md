---
created: 2022-04-22 17:24
modified: 2024-03-19 09:48
tags: [Spring]
---

### 为什么不用 @ControllerAdvice

Spring 提供了 `@ControllerAdvice` 注解，可以处理全局异常。

但是 `@ControllerAdvice` 只能捕获到抛给 Controller 的异常，如果我压根没用 Controller ，比方说我写的是 Webservice 接口，这个注解就不起作用啦。这种情况可以使用 [[AOP]] 来实现全局异常处理。

### 业务场景

最近在写 [[Webservice]] 接口，因为没有做全局异常处理，所以 `logback` 日志没有收集到运行时异常的堆栈信息。而 `@ControllerAdvice` 只能捕获到抛给 Controller 的异常，所以只能寻找别的方法。最后决定使用 `AOP` 来实现。

又因为 Webservice 接口使用了 [[@Transactional]] 管理事务，如果把异常捕获了，没有抛给 Spring，`@Transactional` 就不能回滚事务，所以只能通过 [[AOP#异常通常获取异常|@AfterThrowing]] 来处理异常。这样既能捕获到异常，并调用 `logback` 接口打印日志，又能保证 `@Transactional` 可以回滚事务，两全其美。

### 实现代码

#### 引入依赖

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

#### 定义注解

```java
/**
 * 如果没有回滚事务的要求，只是单纯想打印异常的堆栈信息，使用此注解
 * @since 2022-4-29 17:29
 */
public @interface LogForNoTransaction {
}
```

```java
/**
 * 如果使用了 @Transactional 管理事务，则使用此注解
 * @since 2022-4-22 11:03
 */
public @interface LogForTransaction {
}
```

#### 实现切面

```java
public class ExceptionHandlerAspect {

    @Pointcut("@annotation(com.zdww.image.annotation.LogForNoTransaction)")
    public void logForNoTransaction() {}

    @Pointcut("@annotation(com.zdww.image.annotation.LogForTransaction)")
    public void logForTransaction() {}

    /**
     * 如果没有回滚事务的要求，只是单纯想打印异常的堆栈信息，使用此方法
     * @param pjp
     * @return
     */
    @Around("logForNoTransaction()")
    public Object around(ProceedingJoinPoint pjp) {
        Object result = null;
        try {
            // 执行原有业务
            result = pjp.proceed();
        } catch (Throwable e) {
            String className = pjp.getTarget().getClass().getName();
            String methodName = pjp.getSignature().getName();
            log.error("在{}的{}中，发生了异常：", className, methodName, e);
            return Response.fail();

        return result;
    }

    /**
     * 如果使用了 @Transactional 管理事务，则使用此方法，此方法会将异常抛给 Spring，Spring 就能回滚事务了
     * @param point
     * @param e
     * @throws Exception
     */
    @AfterThrowing(pointcut = "logForTransaction()", throwing = "e")
    public void handle(JoinPoint point, Exception e) throws Exception {
        String className = point.getTarget().getClass().getName();
        String methodName = point.getSignature().getName();
        log.error("在{}的{}中，发生了异常：", className, methodName, e);
        throw new Exception("系统发生了异常", e);
    }
}
```

> [!link] Reference
> [使用aop统一处理controller中的异常及日志](https://cloud.tencent.com/developer/article/1453600)
> 
> [基于spring注解AOP的异常处理](https://developer.aliyun.com/article/465414)
