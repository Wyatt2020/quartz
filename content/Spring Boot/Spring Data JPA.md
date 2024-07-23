---
created: 2023-01-11 22:53
modified: 2023-08-27 23:23
tags: [SpringBoot]
cssclasses:
  - embed-strict
status: todo
---

## 简介

_JPA_ 是 Java Persistence API 的简称，中文名 Java 持久层 API，是 Sun 官方提出的 Java 持久化规范，其设计目标主要是为了简化现有的持久化开发工作和整合 ORM 技术。==JPA 使用 XML 文件或注解（JDK 5.0 或更高版本）来描述对象 - 关联表的映射关系==，能够将运行期的实体对象持久化到数据库，它为 Java 开发人员提供了一种 ORM 工具来管理 Java 应用中的关系数据。 简单地说，将 Java 的普通对象通过对象关系映射（Object-Relational Mapping，ORM）持久化到数据库中。具有易于使用、伸缩性强等优点。

_Spring Data JPA_ 是 Spring 基于 Spring Data 框架、在 JPA 规范的基础上开发的一个框架，使用 Spring Data JPA 可以极大地简化 JPA 的写法，可以在几乎不用写实现的情况下实现对数据库的访问和操作，除了 CRUD 外，还包括分页和排序等一些常用的功能。

## 注解

[[JPA 注解]]

## 参数

`spring.jpa.hibernate.ddl-auto`：是否开启自动更新数据库表结构

- _create_：每次加载 hibernate 时，先删除已存在的数据库表结构再重新生成
- _create-drop_：每次加载 hibernate 时，先删除已存在的数据库表结构再重新生成，并且当 `sessionFactory` 关闭时自动删除生成的数据库表结构
- _update_：只在第一次加载 hibernate 时自动生成数据库表结构，以后再次加载 hibernate 时根据 model 类自动更新表结构（**最常用**）
- _validate_：每次加载 hibernate 时，验证数据库表结构，只会和数据库中的表进行比较，不会创建新表，但是会插入新值
- _none_：关闭自动更新

## Repository 接口

### CrudRepository

提供了一些简单的增删查改功能

```java
public interface CrudRepository<T, ID> extends Repository<T, ID> {
    <S extends T> S save(S entity);

    <S extends T> Iterable<S> saveAll(Iterable<S> entities);

    Optional<T> findById(ID id);

    boolean existsById(ID id);

    Iterable<T> findAll();

    Iterable<T> findAllById(Iterable<ID> ids);

    long count();

    void deleteById(ID id);

    void delete(T entity);

    void deleteAllById(Iterable<? extends ID> ids);

    void deleteAll(Iterable<? extends T> entities);

    void deleteAll();
}
```

### PagingAndSortingRepository

继承于 `CrudRepository`，新增了分页和排序的功能

```java
public interface PagingAndSortingRepository<T, ID> extends CrudRepository<T, ID> {
    Iterable<T> findAll(Sort sort);

    Page<T> findAll(Pageable pageable);
}
```

### JpaRepository

继承于 `PagingAndSortingRepository`，同时，它还继承了另外一个 `QueryByExampleExecutor` 接口，拥有了该接口匹配指定样例的能力

若只是简单的对单表进行 crud 只需要继承 `JpaRepository` 接口，传递两个参数：1.实体类，2.实体类中主键类型

```java
public interface StickerRepository extends JpaRepository<Sticker, Integer> {
}
```

### JpaSpecificationExecutor

继承 `JpaSpecificationExecutor<T>` 接口可实现复杂的查询

- [JPA SpecificationExecutor 使用详解 - 博客园](https://www.cnblogs.com/banywl/articles/14016123.html)
- [SpringData JPA 条件查询、排序、分页查询 - 掘金](https://juejin.cn/post/6985573675764285477)

## JPQL

*JPQL* 是面向对象进行查询的语言，还可以通过自定义的 *JPQL* 完成 *UPDATE* 和 *DELETE* 操作。

*JPQL* 操作注意事项：

1. *JPQL* 不支持使用 *INSERT* 操作

2. *UPDATE* 或 *DELETE* 操作，必须使用 `@Query` 与 `@Modifying` 注解进行修饰

3. *UPDATE* 或 *DELETE* 操作需要使用事务，需要在定义的业务逻辑层（Service 层），在方法上使用 `@Transactional` 注解管理事务

## 自定义查询方法

### 1. 使用 @Query 注解

![[JPA 注解.md#@Query|seamless]]

### 2. 基于方法名规则

对于符合以下命名规则的方法，_Spring Data JPA_ 能够根据其方法名为其自动生成 SQL

| Keyword                | Sample                                                                  | JPQL snippet                                                         |
| ---------------------- | ----------------------------------------------------------------------- | -------------------------------------------------------------------- |
| `Distinct`             | `findDistinctByLastnameAndFirstname`                                    | `select distinct …​ where x.lastname = ?1 and x.firstname = ?2`      |
| `And`                  | `findByLastnameAndFirstname`                                            | `… where x.lastname = ?1 and x.firstname = ?2`                       |
| `Or`                   | `findByLastnameOrFirstname`                                             | `… where x.lastname = ?1 or x.firstname = ?2`                        |
| `Is`, `Equals`         | `findByFirstname`,<br/>`findByFirstnameIs`,<br/>`findByFirstnameEquals` | `… where x.firstname = ?1`                                           |
| `Between`              | `findByStartDateBetween`                                                | `… where x.startDate between ?1 and ?2`                              |
| `LessThan`             | `findByAgeLessThan`                                                     | `… where x.age < ?1`                                                 |
| `LessThanEqual`        | `findByAgeLessThanEqual`                                                | `… where x.age <= ?1`                                                |
| `GreaterThan`          | `findByAgeGreaterThan`                                                  | `… where x.age > ?1`                                                 |
| `GreaterThanEqual`     | `findByAgeGreaterThanEqual`                                             | `… where x.age >= ?1`                                                |
| `After`                | `findByStartDateAfter`                                                  | `… where x.startDate > ?1`                                           |
| `Before`               | `findByStartDateBefore`                                                 | `… where x.startDate < ?1`                                           |
| `IsNull`, `Null`       | `findByAge(Is)Null`                                                     | `… where x.age is null`                                              |
| `IsNotNull`, `NotNull` | `findByAge(Is)NotNull`                                                  | `… where x.age not null`                                             |
| `Like`                 | `findByFirstnameLike`                                                   | `… where x.firstname like ?1`                                        |
| `NotLike`              | `findByFirstnameNotLike`                                                | `… where x.firstname not like ?1`                                    |
| `StartingWith`         | `findByFirstnameStartingWith`                                           | `… where x.firstname like ?1` \(parameter bound with appended `%`\)  |
| `EndingWith`           | `findByFirstnameEndingWith`                                             | `… where x.firstname like ?1` \(parameter bound with prepended `%`\) |
| `Containing`           | `findByFirstnameContaining`                                             | `… where x.firstname like ?1` \(parameter bound wrapped in `%`\)     |
| `OrderBy`              | `findByAgeOrderByLastnameDesc`                                          | `… where x.age = ?1 order by x.lastname desc`                        |
| `Not`                  | `findByLastnameNot`                                                     | `… where x.lastname <> ?1`                                           |
| `In`                   | `findByAgeIn(Collection<Age> ages)`                                     | `… where x.age in ?1`                                                |
| `NotIn`                | `findByAgeNotIn(Collection<Age> ages)`                                  | `… where x.age not in ?1`                                            |
| `True`                 | `findByActiveTrue()`                                                    | `… where x.active = true`                                            |
| `False`                | `findByActiveFalse()`                                                   | `… where x.active = false`                                           |
| `IgnoreCase`           | `findByFirstnameIgnoreCase`                                             | `… where UPPER(x.firstname) = UPPER(?1)`                             |

## Spring Data JPA 搭配 H2 Database

[[H2 数据库#Spring boot 集成 H2 数据库]]

## Spring Data JPA 搭配 MySQL

### application

> [!tip] 
> [Common application properties](https://docs.spring.io/spring-boot/docs/2.0.4.RELEASE/reference/htmlsingle/#common-application-properties)

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mytest
    type: com.alibaba.druid.pool.DruidDataSource
    username: root
    password: root
    driver-class-name: com.mysql.jdbc.Driver //驱动
  jpa:
    hibernate:
      ddl-auto: update //自动更新
    show-sql: true  //日志中显示sql语句
```

> [!info] 参考链接
> 
> - [JPA 官方文档](https://www.tutorialspoint.com/jpa/index.htm)
> - [Spring Data JPA - Reference Documentation](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#reference)
> - [OpenJPA 教程](https://www.hxstrive.com/subject/open_jpa/518.htm)
> - [JPA 教程](https://www.yiibai.com/jpa/jpa-introduction.html)
> - [JPA 教程\_w3cschool](https://www.w3cschool.cn/java/jpa-entitymanager.html)
> - [使用 JPA 操作数据库 - CSDN 博客](https://blog.csdn.net/pengjunlee/article/details/80038677)
> - [Spring Data JPA 使用:看这一篇就够了 - 个人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000037755804)
> - [Spring Data Jpa(2019_idea版) - 哔哩哔哩](https://www.bilibili.com/video/BV1Y4411W7Rx?p=1&vd_source=7684be089e60d522b937867a63e07bf7)
