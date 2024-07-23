---
created: 2023-11-16 16:47
modified: 2023-11-16 16:47
tags:
  - MySQL
---

> [!link]- Reference
> - [基于 ShardingSphere-JDBC 的 MySQL 读写分离](https://mp.weixin.qq.com/s/1exvst1BP0ebCRZfxaNckg)
> - [Sharding-JDBC 实现读写分离](https://www.cnblogs.com/huanshilang/p/12055296.html)
> - [基于 aop 的 mysql 实现读写分离](https://www.cnblogs.com/wyq178/p/13352707.html)

> [!info] 基于 ShardingSphere-JDBC
> Spring Boot version：2.2.5. RELEASE

#### pom

```xml
<!--mybatis-->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.2</version>
</dependency>
<!--mysql 驱动-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
<!--druid 数据库连接池-->
<!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.21</version>
</dependency>
<!--shardingsphere-->
<dependency>
    <groupId>org.apache.shardingsphere</groupId>
    <artifactId>sharding-jdbc-spring-boot-starter</artifactId>
    <version>4.1.1</version>
</dependency>
```

#### application. yml

```yaml
spring:
  shardingsphere:
    # 数据源相关配置
    datasource:
      # 数据源名称
      names: master,s1
      # MySQL master 数据源
      master:
        type: com.alibaba.druid.pool.DruidDataSource
        driver-class-name: com.mysql.cj.jdbc.Driver
        url: jdbc:mysql://47.108.77.227:3306/myblog?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone=UTC&serverTimezone=GMT%2B8
        username: root
        password: 123456

        #druid 数据源专有配置
        initialSize: 5
        minIdle: 5
        maxActive: 20
        maxWait: 60000
        timeBetweenEvictionRunsMillis: 60000
        minEvictableIdleTimeMillis: 300000
        validationQuery: SELECT 1 FROM DUAL
        testWhileIdle: true
        testOnBorrow: false
        testOnReturn: false
        poolPreparedStatements: true
      # MySQL slave 数据源
      s1:
        type: com.alibaba.druid.pool.DruidDataSource
        driver-class-name: com.mysql.cj.jdbc.Driver
        url: jdbc:mysql://47.110.239.230:3306/myblog?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone=UTC&serverTimezone=GMT%2B8
        username: root
        password: 123456

        #druid 数据源专有配置
        initialSize: 5
        minIdle: 5
        maxActive: 20
        maxWait: 60000
        timeBetweenEvictionRunsMillis: 60000
        minEvictableIdleTimeMillis: 300000
        validationQuery: SELECT 1 FROM DUAL
        testWhileIdle: true
        testOnBorrow: false
        testOnReturn: false
        poolPreparedStatements: true

    masterslave:
      # 读写分离配置：ROUND_ROBIN(轮询)，RANDOM（随机）
      load-balance-algorithm-type: round_robin
      # 最终的数据源名称
      name: dataSource
      # 主库数据源名称
      master-data-source-name: master
      # 从库数据源名称列表，多个逗号分隔
      slave-data-source-names: s1

    # 其他属性
    props:
      # 开启SQL显示
      sql.show: true
```

当然，还需要配置好 `log4j`，才能在控制台看到日志

效果如下：从 `s1` 从库读取，写入数据到主库 `master`，说明成功了！

![[MySQL 读写分离.png]]
