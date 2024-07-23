---
created: 2022-06-08 10:48
tags:
  - Spring/注解
  - SSM
---

## Spring Boot

引入依赖
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

## SSM 项目

1. 引入依赖
```xml
<dependency>
  <groupId>org.aspectj</groupId>
  <artifactId>aspectjweaver</artifactId>
  <version>1.8.7</version>
</dependency>
```

2. 在 `spring-mvc.xml` 配置，启用 `@Aspect` 注解支持
```xml
<aop:aspectj-autoproxy/>
```

也可以在 `bean 配置类` 或 `Spring Boot 主启动类` 使用 `@EnableAspectJAutoProxy` 注解启用代理

3. 编写切面类
```java
@Aspect
@Component
public class RepeatSubmitAspect {
    @Autowired
    private RedisUtil redisUtil;
    private final String POINT_CUT = "@annotation(com.annotation.NoRepeatSubmit)";
//    private final String POINT_CUT = "execution(* com.controller.*.*(..))";

    @Pointcut(POINT_CUT)
    public void getLsh() {}

    @Around("getLsh()")
    public Object around(ProceedingJoinPoint pjp) {
        Object result = null;
//        MethodSignature ms = (MethodSignature)pjp.getSignature();
//        Method method = ms.getMethod();
//        boolean active = method.getAnnotation(NoRepeatSubmit.class) != null;
        String lsh = (String) pjp.getArgs()[0];
        String isExist = (String) redisUtil.get(lsh);
        if ("1".equals(isExist)) {
            Map<String, String> errorResult = new HashMap<>();
            errorResult.put("msg", "重复的请求");
            System.out.println("重复的请求");
            return errorResult;
        }
        redisUtil.set(lsh, "1");
        try {
            result = pjp.proceed();
        } catch (Throwable throwable) {
            throw new RuntimeException("切面异常");
        }
        return result;
    }
}
```

4. 测试类
```java
@RestController
public class TestController {
    @Autowired
    private RedisUtil redisUtil;

    @NoRepeatSubmit
    @GetMapping("/refund/{lsh}")
    public Map<String, String> refund(@PathVariable String lsh) {
        Map<String, String> result = new HashMap<>();
        System.out.println("===进入refund方法");
//        redisUtil.remove(lsh);
        result.put("msg","success");
        return result;
    }
}
```