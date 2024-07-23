---
created: 2022-08-19 17:06
modified: 2024-03-19 10:13
tags: [Spring]
---

## 什么是 AOP

AOP（_Aspect Oriented Programming_） 叫做面向切面编程，指在程序运行期间动态的将某段代码切入到指定方法指定位置进行运行的操作。如：性能监控、日志记录、权限控制等，通过 AOP 解决代码耦合问题，让方法的职责更加单一。

## AOP 术语

### 连接点（Joinpoint）

连接点的最小单位称之为方法，每一个方法称之为连接点，如类开始初始化前、类初始化后、类某个方法调用前、调用后、方法抛出异常后。一个类或一段程序代码拥有一些具有边界性质的特定点，这些点中的特定点就称为“连接点”。比如，`@Before`、`@After` 等注解所在的方法都称之为连接点。

### 切点（Pointcut）

==切点是连接点的集合==，每个程序类都拥有多个连接点，如一个拥有两个方法的类，这两个方法都是连接点，即连接点是程序类中客观存在的事物。AOP 通过“切点”定位特定的连接点。连接点相当于数据库中的记录，而切点相当于查询条件。切点和连接点不是一对一的关系，一个切点可以匹配多个连接点。比如，`@Pointcut("execution(* com.star.service…(..))")` 就是一个切点。

### 通知（Advice）

切入连接点的时机和切入连接点的内容称为通知，Spring AOP 有 5 种通知类型：

- **前置通知 @Before**：在目标方法**调用前**调用

- **返回通知 @AfterReturning**：在目标方法**返回后**调用

- **异常通知 @AfterThrowing**：在目标方法**异常后**调用

- **后置通知 @After**：调用在目标方法**返回或异常后**调用

- **环绕通知 @Around**：将目标方法封装起来，可以**实现以上四种通知的功能**

- *执行顺序*：前置 →返回（异常）→后置。（返回和异常是互斥的）

> 通知定义了需要做什么，以及在某个连接点的什么时候做。 上面的切点定义了在哪里做。

### 目标对象（Target）

被增强的对象，也就是被通知的对象。

### 引介（Introduction）

允许我们向现有的类添加新方法属性。通过引介，我们可以动态地为该业务类添加接口的实现逻辑，让业务类成为这个接口的实现类。

### 织入（Weaving）

织入是将通知添加到目标类具体连接点上的过程。AOP 像一台织布机，将目标类、通知或引介通过 AOP 这台织布机天衣无缝地编织到一起。根据不同的实现技术，AOP 有三种织入的方式：

1. 编译期织入，这要求使用特殊的 Java 编译器。
2. 类装载期织入，这要求使用特殊的类装载器。
3. 动态代理织入，在运行期为目标类添加通知生成子类的方式。

> 把切面应用到目标对象来创建新的代理对象的过程，==Spring 采用动态代理织入，而 AspectJ 采用编译期织入和类装载期织入。==

### 代理对象（Proxy）

一个类被 AOP 织入通知后，就产出了一个结果类，这个类就是代理对象，它是融合了原类和通知逻辑的代理类。

### 切面（Aspect）

连接点和切点以及通知所在的那个类称为切面，它既包括了横切逻辑的定义，也包括了连接点的定义，Spring AOP 就是负责实施切面的框架，它将切面所定义的横切逻辑织入到切面所指定的连接点中。

> 切点的通知的结合，切面知道所有它需要做的事：何时/何处/做什么

## JoinPoint

- 在切面方法中添加 **JoinPoint** 参数（**必须要把他们放在第一位！**），就可以获取到封装了该方法信息的 JoinPoint 对象。

- 适用场景：前置通知、后置通知、异常通知、返回通知

### 常用方法

| 方法名                        | 功能                                          |
| -------------------------- | ------------------------------------------- |
| `Signature getSignature()` | 获取封装了署名信息的对象，在该对象中可以获取到目标方法名，所属类的 Class 等信息 |
| `Object[] getArgs()`       | 获取传入目标方法的参数数组                               |
| `Object getTarget()`       | 获取目标对象 (被代理对象)                               |
| `Object getThis()`         | 获取代理对象                                      |

- 目标方法名： `joinPoint.getSignature().getName());`
- 目标方法所属类的简单类名：`joinPoint.getSignature().getDeclaringType().getSimpleName());`
- 目标方法所属类的类名：`joinPoint.getSignature().getDeclaringTypeName();`
- 目标方法声明类型：`Modifier.toString(joinPoint.getSignature().getModifiers()));`

## ProceedingJoinPoint

- `ProceedingJoinPoint` 接口是 `JoinPoint` 的子接口。

- 适用场景：**只用在环绕通知中**。

> [!tip] `ProceedingJoinPoint` 和 `JoinPoint` 的区别
> 在 Spring AOP 中，JoinPoint 和 ProceedingJoinPoint 都是 AspectJ 库中的接口，它们都用于描述程序执行过程中的连接点（即切入点），但两者之间存在一定的区别：
> 1. `JoinPoint`：
> 	- 是 AOP 的基本概念，它代表了程序执行过程中可能插入切面代码的一个点，比如一个方法调用、字段访问等。
> 	- 提供了获取当前连接点信息的方法，如方法签名（`getSignature()`）、目标对象（`getTarget()`）、方法参数（`getArgs()`）等。
> 	- 但它本身并不支持对目标方法的调用控制，也就是说，你不能通过 JoinPoint 实例来决定是否继续执行目标方法或者改变其行为。
> 2. `ProceedingJoinPoint`：
> 	- 是 JoinPoint 接口的子接口，继承了 JoinPoint 的所有功能，并在此基础上增加了一个关键方法：`proceed()`。
> 	- `proceed()` 方法允许在环绕通知（Around Advice）中调用以触发原始目标方法的执行，并可以控制其执行流程。例如，可以在方法执行前后添加额外逻辑，或者根据条件决定是否应该执行目标方法以及如何处理返回结果。
> 	- 在环绕通知中使用 ProceedingJoinPoint 可以灵活地实现前置处理、后置处理、异常处理以及最终处理等多个阶段的拦截和操作。
> 
> 总结来说，如果你想在切面中完全控制被代理方法的执行流程（包括决定是否执行、何时执行、如何修改执行结果等），那么你需要使用 ProceedingJoinPoint 并在环绕通知中定义相应的逻辑。而如果只是想在方法执行前后获得一些上下文信息，则 JoinPoint 就足够了。

### proceed 方法

- `proceed()` 方法是有两个构造方法的
	- 调用**无参数的** proceed，当原始方法有参数，会在调用的过程中自动传入参数
	- 但是当需要修改原始方法的参数时，就只能采用**有参数的** proceed
  ```java
  Object proceed() throws Throwable //执行目标方法
  Object proceed(Object[] var1) throws Throwable //传入的新的参数去执行目标方法
  ```
- 有了这个特性后，我们就可以在环绕通知中对原始方法的参数进行拦截过滤：如去除字符串最后一位的空格等操作。

## 获取返回值

只有**返回通知** `@AfterReturing` 和**环绕通知** `@Around` 这两个通知类型可以获取返回值。

### 环绕通知获取返回值

使用 `Object result = pjp.proceed( );` 获取返回值，`result` 就是原始方法的返回值，我们不但可以获取，如果需要还可以进行修改。

### 返回通知获取返回值

在 `@AfterReturning` 注解中，使用  `returning`  指定返回值名称，且需要与返回通知方法中形参名称保持一致。

```java
@AfterReturning(value = "pt1()", returning = "ret")
public void afterReturning(Object ret) {
    System.out.println("返回通知，获取原始方法返回值--->" + ret);
}
```

## 获取异常

只有**异常通知** `@AfterThrowing` 和**环绕通知** `@Around` 这两个通知类型可以获取异常。

### 环绕通知获取异常

使用 `try-catch` 语句就能获取异常了，异常处理在 `catch` 中进行。

```java
try{
    ret = pjp.proceed();
}catch(Throwable e){
    //根据业务需求处理异常
}
```

### 异常通常获取异常

异常通常和返回通知类似，也要在注解上加一句话。不过 `@AfterThrowing`  注解中是使用  `throwing`  指定异常名称，需要与方法形参名相同。

```java
@AfterThrowing(value = "pt1()",throwing = "t")
public void afterThrowing(Throwable t) {
    System.out.println("异常通知获取到的异常" + t);
}
```

> [!info] 参考链接
> 
> - [浅谈 AOP 以及 AspectJ 和 Spring AOP](https://segmentfault.com/a/1190000020621578)
> - [Spring AOP 通知中获取数据](https://juejin.cn/post/7110239035653619748)
> - [Spring 框架底层原理- AOP](https://onestar.blog.csdn.net/article/details/112387588)
