---
created: 2022-05-26 16:31
tags:
  - Spring/注解
---

## 作用

使用 `@Transactional` 开启基于 AOP 声明式事务，将具体业务逻辑与事务处理解耦

## 注解属性

**`value / transactionManager`**
- 当配置了多个事务管理器时，可以使用该属性指定选择哪个事务管理器

**`propagation`**
- [事务的传播行为](#Spring%20事务传播行为) ，**默认值为 Propagation.REQUIRED**

**`isolation`**
- [事务的隔离级别](#Spring%20事务隔离级别) ，**默认值为 Isolation.DEFAULT**

**`timeout`**
- 事务的超时时间，默认值为`-1`
- 如果超过该时间限制但事务还没有完成，则自动回滚事务

**`readOnly`**
- 指定事务是否为只读事务，默认值为 `false`；
- 为了忽略那些不需要事务的方法，比如读取数据，可以设置 `read-only` 为 `true`

**`rollbackFor`**
- 抛出指定的异常类型，回滚事务，可以指定多个异常类型

**`noRollbackFor`**
- 抛出指定的异常类型，不回滚事务，可以指定多个异常类型

## 使用位置

`@Transactional` 可以作用于**接口**、**接口方法**、**类**以及**类方法**

- 当作用于类上时，该类的所有 `public` 方法将都具有该类型的事务属性
- 当作用于方法时，如果类也配置了 `@Transactional`，那么方法的事务会覆盖类的事务配置信息
- **Spring 不建议在接口或者接口方法上使用该注解**，因为这只有在使用基于接口的代理时它才会生效

```java
// 所有操作都成功才会提交事务，否则就回滚
@Transactional
public int insertUser(User user)
{
    int rows = userMapper.insertUser(user);
    insertUserRole(user);
    return rows;
}
```

## rollbackFor

Spring 默认的事务规则是遇到 `运行时异常（RuntimeException）` 和 `程序错误（Error）` 才会回滚。如果想针对检查异常进行事务回滚，可以在 `@Transactional` 注解里使用 `rollbackFor` 属性明确指定异常

```java
@Transactional(rollbackFor = Exception.class)
public int insertUser(User user) throws Exception
{
    int rows = userMapper.insertUser(user);
    insertUserRole(user);
    // 模拟抛出SQLException异常
    boolean flag = true;
    if (flag)
    {
        throw new SQLException("发生异常了..");
    }
    return rows;
}
```

如果 `service` 层的方法有非运行时异常，必须用 `try...catch` 捕获异常，然后再次抛出，否则事务不会回滚

```java
@Transactional(IOException.class)
public int insertUser(User user) {
    try {
        // dosomething
    } catch (IOException e) {
        throw e;
    }
}
```

## Spring 事务传播行为

| 值                                      | 说明                                                                                 |
| :-------------------------------------- | ------------------------------------------------------------------------------------ |
| **Propagation.REQUIRED（Spring 默认）** | 使用当前的事务，如果当前不存在事务，则创建一个新的事务                               |
| `Propagation.SUPPORTS`                    | 使用当前的事务，如果当前不存在事务，则以非事务的方式继续运行                         |
| `Propagation.MANDATORY`                   | 使用当前的事务，如果当前不存在事务，则抛出异常                                       |
| `Propagation.REQUIRES_NEW`                | 新建事务，如果当前存在事务，把当前事务挂起                                           |
| `Propagation.NOT_SUPPORTED`               | 以非事务的方式运行，如果当前存在事务，把当前事务挂起                                 |
| `Propagation.NEVER`                       | 以非事务的方式运行，如果当前存在事务，则抛出异常                                     |
| `Propagation.NESTED`                      | 如果当前存在事务，则在嵌套事务内执行；如果当前没有事务，则执行与 REQUIRED 类似的操作 |

## Spring 事务隔离级别

| 值                                          | 说明                                                                                |
| ------------------------------------------- | ----------------------------------------------------------------------------------- |
| **Isolation.DEFAULT（Spring 默认）**        | 使用数据库本身使用的隔离级别 ORACLE（读已提交） MySQL（可重复读）                   |
| `Isolation.READ_UNCOMMITTED`                  | 【读未提交】最低的隔离级别（脏读、不可重复读、幻读）                                |
| `Isolation.READ_COMMITTED`（**ORACLE 默认**） | 【读已提交】ORACLE 默认隔离级别（不可重复读、幻读）                                 |
| `Isolation.REPEATABLE_READ`（**MySQL 默认**） | 【可重复读】会出现幻读                                                              |
| `Isolation.SERIALIZABLE`                      | 【串行化】最高的隔离级别，要求所有的 SQL 都会按照顺序执行，能够完全保证数据的一致性 |

> [!note] 知识联动
[MySQL 的隔离级别](MySQL%20的隔离级别.md)


## 注意事项

> [!attention]
>- **在同一个类里面调用使用了 @Transactional 的方法， @Transactional 无效**。此时需要使用 [解决同类中调用嵌套方法导致 AOP 失效](解决同类中调用嵌套方法导致%20AOP%20失效.md)
>- 使用 Java 锁时需要注意：由于 Spring 事务是通过 AOP 实现的，所以在方法执行之前会有开启事务，之后会有提交事务逻辑。而 synchronized 代码块执行是在事务之内执行的，可以推断在 synchronized 代码块执行完时，事务还未提交，其他线程进入 synchronized 代码块后，读取的数据不是最新的。**所以必须使 synchronized 锁的范围大于事务控制的范围，把 synchronized 加到 Controller 层或者大于事务边界的调用层**


> [!info]  参考链接
> - [Spring 事务传播行为详解](https://segmentfault.com/a/1190000013341344)
> - [SpringBoot 使用 @Transactional 注解配置事务](https://blog.csdn.net/Abysscarry/article/details/80189232)
> - [Spring 事务的介绍，以及基于注解 @Transactional 的声明式事务](https://www.cnblogs.com/java-wang/p/15496974.html)
> - [Java 异常总结和 Spring 事务处理异常机制浅析](https://cloud.tencent.com/developer/article/1589894)

