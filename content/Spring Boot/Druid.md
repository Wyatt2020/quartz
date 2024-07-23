---
created: 2023-11-24 12:03
modified: 2023-11-24 12:03
tags:
  - 数据库
related:
  - "[[Spring Boot]]"
  - "[[Spring]]"
---

> [!summary] Druid 是 Java 语言中最好的数据库连接池。Druid 能够提供强大的监控和扩展功能。

> [!tip] Spring Boot 2.0 以上默认使用 `com.zaxxer.hikari.HikariDataSource` 数据源，但可以通过 `spring.datasource.type` 指定数据源。

> [!github] 
>
> - [druid](https://github.com/alibaba/druid)
> 	- [官方文档](https://github.com/alibaba/druid/wiki/%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98)
> - [druid-spring-boot-starter](https://github.com/alibaba/druid/tree/master/druid-spring-boot-starter)
> 	- [查看最新版](https://mvnrepository.com/artifact/com.alibaba/druid-spring-boot-starter)

> [!link]- Reference
> [Spring Boot 2.x 自定义数据源 DruidDataSource（操作 MySQL 数据库）](https://blog.csdn.net/wangmx1993328/article/details/81865153)
> 
> [Spring Boot 整合 Druid 指南](https://developer.aliyun.com/article/1157595)

## Spring Boot

### Maven

```xml
<dependency>
   <groupId>com.alibaba</groupId>
   <artifactId>druid-spring-boot-starter</artifactId>
   <version>${druid-version}</version>
</dependency>
```

### application. yml

> [!tip] 
> [参考配置 1](https://gitee.com/wangmx1993/red-door/blob/master/src/main/resources/application.yml)
> 
> [参考配置 2](https://github.com/alibaba/druid/wiki/DruidDataSource%E9%85%8D%E7%BD%AE)
> 
> [Druid 中使用 log4j2 进行日志输出](https://github.com/alibaba/druid/wiki/Druid%E4%B8%AD%E4%BD%BF%E7%94%A8log4j2%E8%BF%9B%E8%A1%8C%E6%97%A5%E5%BF%97%E8%BE%93%E5%87%BA)

#### 开箱即用

```yaml
spring:  
  datasource:  
    username: ENC(HfL8T3J805i6I8WYfgZ3mg==)  
    password: ENC(pOlhM6SaHv5jhKl5dxtdtg==)  
    url: jdbc:mysql://127.0.0.1:3306/database1123?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone=Asia/Shanghai  
    driver-class-name: com.mysql.cj.jdbc.Driver  
    type: com.alibaba.druid.pool.DruidDataSource  
  
    # Druid 数据源专有配置,对应的是 com.alibaba.druid.pool.DruidDataSource 中的属性  
    druid:  
      # 数据源名称：当存在多个数据源时，设置名字可以很方便的来进行区分，默认自动生成名称，格式是："DataSource-" + System.identityHashCode(this)  
      name: druid-db1  
  
      ########## 过滤器 ##########      # 配置监控统计拦截的filters，去掉后监控界面sql无法统计。stat:监控统计 log4j:日志记录 wall:防御sql注入  
      # 如果运行时报错：ClassNotFoundException:orgapache.log4j.Priority，则导入log4j依赖即可  
      filters: stat,wall  
  
      ########## WebStatFilter：web 状态监控过滤器 ##########      web-stat-filter:  
        enabled: true  
        url-pattern: /*                 # 过滤所有 url        exclusions: "*.js,*.css,*.gif,*.jpg,*.png,*.ico,/druid/*"   # 排除一些不必要的 url        session-stat-enable: true       # 开启 session 统计功能  
        session-stat-max-count: 1000    # session 的最大个数，默认100  
  
      ########## StatViewServlet(监控页面路径配置)，用于展示 Druid 的监控统计信息 ##########      stat-view-servlet:  
        enabled: true  
        url-pattern: /druid/*           # 访问内置监控页面的路径，内置监控页面的首页是 http://127.0.0.1:8080/druid/login.html 或者 /druid/index.html        reset-enable: false             # 不允许清空统计数据，重新计算  
        login-username: root            # 配置监控页面登陆用户  
        login-password: 12345           # 配置监控页面登陆密码  
        allow: 127.0.0.1                # 允许访问的地址，如果没有配置或者为空，则允许所有访问  
        deny:                           # 拒绝访问的地址，deny 优先于 allow，如果在 deny 列表中，就算在 allow 列表中，也会被拒绝  
      aop-patterns: com.wmx..*.controller..*.* # Spring 监控 AOP 切入点，多个时用英文逗号分隔
```

#### 详细配置说明

```yaml
spring:  
  datasource:  
    # jdbc 通用配置,对应的是 org.springframework.boot.autoconfigure.jdbc.DataSourceProperties 中的属性  
    username: root  
    password: 12345  
    # 高版本的 Spring Boot 搭配的 MySQL 驱动版本通常也较高，如 mysql-connector-java:8.0.X    # 此时 url 的值要带时区 serverTimezone、driver-class-name 的值要带 cj    url: jdbc:mysql://127.0.0.1:3306/database1123?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone=Asia/Shanghai  
    driver-class-name: com.mysql.cj.jdbc.Driver  
    # 切换 Druid 数据源  
    type: com.alibaba.druid.pool.DruidDataSource  
  
    # Druid 数据源专有配置,对应的是 com.alibaba.druid.pool.DruidDataSource 中的属性  
    druid:  
      # 数据源名称：当存在多个数据源时，设置名字可以很方便的来进行区分，默认自动生成名称，格式是："DataSource-" + System.identityHashCode(this)  
      name: druid-db1  
      initial-size: 10 # 初始化时建立物理连接的个数。初始化发生在显示调用 init 方法，或者第一次 getConnection 时，默认0  
      min-idle: 200    # 最大连接池数量，默认8  
      max-active: 10   # 最小连接池数量  
      maxWait: 60000   # 获取连接时最大等待时间，单位毫秒。配置了 maxWait 之后，缺省启用公平锁，并发效率会有所下降，如果需要可以通过配置 useUnfairLock 属性为 true 使用非公平锁。  
      timeBetweenEvictionRunsMillis: 60000 # 1)Destroy线程会检测连接的间隔时间 2)testWhileIdle的判断依据(详细看testWhileIdle属性的说明)  
      minEvictableIdleTimeMillis: 300000   # 设置连接最小可收回空闲时间(毫秒)，默认为 1000L * 60L * 30L      maxEvictableIdleTimeMillis: 900000   # 设置连接最大可收回空闲时间(毫秒)，默认为 1000L * 60L * 60L * 7      validationQuery: SELECT 1 FROM DUAL # 用来检测连接是否有效的sql,求是一个查询语句。默认为null，此时testOnBorrow、testOnReturn、testWhileIdle都不会其作用。  
      testWhileIdle: true # 申请连接的时候检测,如果空闲时间大于 timeBetweenEvictionRunsMillis，执行 validationQuery 检测连接是否有效，默认为 true      testOnBorrow: false # 申请连接时执行 validationQuery 检测连接是否有效，做了这个配置会降低性能，默认 false.      testOnReturn: false # 归还连接时执行 validationQuery 检测连接是否有效，做了这个配置会降低性能，默认 false.      poolPreparedStatements: true # 是否缓存 preparedStatement，也就是 PSCache。PSCache 对支持游标的数据库性能提升巨大，比如说 oracle，在 MySQL 下建议关闭。  
      # 要启用 PSCache，必须配置大于0，当大于0时， poolPreparedStatements 自动触发修改为 true      # 在 Druid 中，不会存在 Oracle 下 PSCache 占用内存过多的问题，可以把这个数值配置大一些，比如说100  
      maxOpenPreparedStatements: 20  
      # 连接池中的 minIdle 数量以内的连接，空闲时间超过 minEvictableIdleTimeMillis，则会执行 keepAlive 操作  
      keepAlive: true  
      maxPoolPreparedStatementPerConnectionSize: 20 # 每个连接大小的最大池准备语句数  
      useGlobalDataSourceStat: true # 是否使用全局数据源统计，默认 false      connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500 # 连接属性  
  
      ########## 过滤器 ##########      #配置监控统计的内置过滤器：stat-监控统计(必须配置，否则监控不到sql)、wall-防御sql注入、log4j2-日志记录框架(值与应用中的日志框架保持一致，如 log4j、log4j2、slf4j等等)  
      filters: stat,wall,log4j2  
      # 自定义过滤器配置：stat、slf4j、log4j、log4j2、commons-log、wall  
      # 内置 Filter 都是默认配置，无法满足需求时，则可以自定义 Filter，自定义的过滤器默认都是没有开启的。  
      filter:  
        stat: # 状态监控过滤器  
          enabled: true  
          db-type: mysql  
          log-slow-sql: true  # 开启慢sql监控，超过指定时间则认为是慢sql，记录到日志中  
          slow-sql-millis: 5000  
        slf4j: # 日志监控过滤器，使用 slf4j 进行日志输出  
          enabled: true  
          statement-log-error-enabled: true  
          statement-create-after-log-enabled: false  
          statement-close-after-log-enabled: false  
          result-set-open-after-log-enabled: false  
          result-set-close-after-log-enabled: false  
        wall: # 防火墙过滤器  
          enabled: true  
          config:  
            delete-allow: false     # 不允许删除数据  
            drop-table-allow: false # 不允许删除表  
  
      ########## WebStatFilter：web 状态监控过滤器 ##########      web-stat-filter:  
        enabled: true  
        url-pattern: /*                 # 过滤所有 url        exclusions: "*.js,*.css,*.gif,*.jpg,*.png,*.ico,/druid/*"   # 排除一些不必要的 url        session-stat-enable: true       # 开启 session 统计功能  
        session-stat-max-count: 1000    # session 的最大个数，默认100  
  
      ########## StatViewServlet(监控页面路径配置)，用于展示 Druid 的监控统计信息 ##########      stat-view-servlet:  
        enabled: true  
        url-pattern: /druid/*           # 访问内置监控页面的路径，内置监控页面的首页是 http://127.0.0.1:8080/druid/login.html 或者 /druid/index.html        reset-enable: false             # 不允许清空统计数据，重新计算  
        login-username: root            # 配置监控页面登陆用户  
        login-password: 123456          # 配置监控页面登陆密码  
        allow: 127.0.0.1                # 允许访问的地址，如果没有配置或者为空，则允许所有访问  
        deny:                           # 拒绝访问的地址，deny 优先于 allow，如果在 deny 列表中，就算在 allow 列表中，也会被拒绝  
      aop-patterns: com.wmx..*.controller..*.* # Spring 监控 AOP 切入点，多个时用英文逗号分隔
```

## Spring

### Maven

```xml
<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>druid</artifactId>
	<version>${druid-version}</version>
</dependency>
```

## 属性配置

> [!github] [DruidDataSource 配置属性列表](https://github.com/alibaba/druid/wiki/DruidDataSource%E9%85%8D%E7%BD%AE%E5%B1%9E%E6%80%A7%E5%88%97%E8%A1%A8)
> DruidDataSource 配置兼容 DBCP，但个别配置的语意有所区别。

| 配置 | 缺省值 | 说明 |
| --- | --- | --- |
| name |  | 配置这个属性的意义在于，如果存在多个数据源，监控的时候可以通过名字来区分开来。如果没有配置，将会生成一个名字，格式是：`DataSource-` + `System.identityHashCode(this)`.另外配置此属性至少在 1.0.5 版本中是不起作用的，强行设置 `name` 会出错。[详情-点此处](http://blog.csdn.net/lanmo555/article/details/41248763)。 |
| url |  | 连接数据库的 `url`，不同数据库不一样。例如：`jdbc:mysql://10.20.153.104:3306/druid2` `jdbc:oracle:thin:@10.20.149.85:1521:ocnauto` |
| username |  | 连接数据库的用户名 |
| password |  | 连接数据库的密码。如果你不希望密码直接写在配置文件中，可以使用 ConfigFilter。[详细看这里](https://github.com/alibaba/druid/wiki/%E4%BD%BF%E7%94%A8ConfigFilter) |
| driverClassName | 根据 `url` 自动识别 | 这一项可配可不配，如果不配置 druid 会根据 `url` 自动识别 dbType，然后选择相应的 `driverClassName` |
| initialSize | 0 | 初始化时建立物理连接的个数。初始化发生在显示调用 init 方法，或者第一次 getConnection 时 |
| maxActive | 8 | 最大连接池数量 |
| <font color="#ff0000">maxIdle</font> | <font color="#ff0000">8</font> | <font color="#ff0000">已经不再使用，配置了也没效果</font> |
| minIdle |  | 最小连接池数量 |
| maxWait |  | 获取连接时最大等待时间，单位毫秒。配置了 `maxWait` 之后，缺省启用公平锁，并发效率会有所下降，如果需要可以通过配置 `useUnfairLock` 属性为 `true` 使用非公平锁。 |
| poolPreparedStatements | false | 是否缓存 preparedStatement，也就是 PSCache。PSCache 对支持游标的数据库性能提升巨大，比如说 oracle。在 mysql 下建议关闭。 |
| maxPoolPreparedStatementPerConnectionSize | -1 | 要启用 PSCache，必须配置大于 0，当大于 0 时，`poolPreparedStatements` 自动触发修改为 `true`。在 Druid 中，不会存在 Oracle 下 PSCache 占用内存过多的问题，可以把这个数值配置大一些，比如说 `100` |
| validationQuery |  | 用来检测连接是否有效的 sql，要求是一个查询语句，常用 `select 'x'`。如果 `validationQuery` 为 `null`，`testOnBorrow`、`testOnReturn`、`testWhileIdle` 都不会起作用。 |
| validationQueryTimeout |  | 单位：秒，检测连接是否有效的超时时间。底层调用 jdbc Statement 对象的 `void setQueryTimeout (int seconds)`方法 |
| testOnBorrow | true | 申请连接时执行 `validationQuery` 检测连接是否有效，做了这个配置会降低性能。 |
| testOnReturn | false | 归还连接时执行 `validationQuery` 检测连接是否有效，做了这个配置会降低性能。 |
| testWhileIdle | false | 建议配置为 `true`，不影响性能，并且保证安全性。申请连接的时候检测，如果空闲时间大于 `timeBetweenEvictionRunsMillis`，执行 `validationQuery` 检测连接是否有效。 |
| keepAlive | false（1.0.28） | 连接池中的 `minIdle` 数量以内的连接，空闲时间超过 `minEvictableIdleTimeMillis`，则会执行 `keepAlive` 操作。 |
| timeBetweenEvictionRunsMillis | 1 分钟（1.0.14） | 有两个含义：1) Destroy 线程会检测连接的间隔时间，如果连接空闲时间大于等于 `minEvictableIdleTimeMillis` 则关闭物理连接。2) `testWhileIdle` 的判断依据，详细看 `testWhileIdle` 属性的说明 |
| numTestsPerEvictionRun | 30 分钟（1.0.14） | 不再使用，一个 DruidDataSource 只支持一个 EvictionRun |
| minEvictableIdleTimeMillis |  | 连接保持空闲而不被驱逐的最小时间 |
| connectionInitSqls |  | 物理连接初始化的时候执行的 sql |
| exceptionSorter | 根据 dbType 自动识别 | 当数据库抛出一些不可恢复的异常时，抛弃连接 |
| filters |  | 属性类型是字符串，通过别名的方式配置扩展插件，常用的插件有：监控统计用的 `filter:stat` `日志用的 filter:log4j` `防御 sql 注入的 filter:wall` |
| proxyFilters |  | 类型是 `List<com.alibaba.druid.filter.Filter>`，如果同时配置了 `filters` 和 `proxyFilters`，是组合关系，并非替换关系 |

