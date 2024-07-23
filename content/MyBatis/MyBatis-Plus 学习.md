---
created: 2022-06-08 14:41
tags:
  - MyBatis
related:
  - "[[Spring]]"
---
> [!link] 官方
> [官方文档](https://baomidou.com/)
> - [使用配置 | MyBatis-Plus](https://baomidou.com/reference/)
> 
> [更新日志](https://github.com/baomidou/mybatis-plus/blob/3.0/CHANGELOG.md)

> [!tip] 
> *MyBatis-Plus 3.0* 版本基于 JDK 8

### 常用注解

> [!link] Reference
> [注解配置 | MyBatis-Plus](https://baomidou.com/reference/annotation/)

MyBatis-Plus 通过扫描实体类，并基于[[Java 反射|反射]] 获取实体类信息作为数据库表信息。
- 类名驼峰转下刘线作为表名
- 名为 `id` 的字段作为主键
- 变量名驼峰转下刘线作为表的字段名

#### @TableName

指定表名

#### @TableId

指定表中的主键字段信息

`IdType` 枚举：
- `AUTO`：数据库自增长
- `INPUT`：通过 set 方法自行输入
- `ASSIGN ID`：分配 ID，接口 `IdentifierGenerator` 的方法 `nextld` 来生成 id，默认实现类为 DefaultldentifierGenerator 雪花算法

#### @TableField

指定表中的普通字段信息

`exist=false`：标记此成员属性不是数据库字段

使用场景
- 成员变量名与数据库字段名不一致
- 成员变量名以 `is` 开头，且是布尔值
- 成员变量名与数据库关键字冲突
- 成员变量不是数据库字投

### 配置



### Maven

```xml
<!-- MyBatis-Plus -->  
<dependency>  
    <groupId>com.baomidou</groupId>  
    <artifactId>mybatis-plus-boot-starter</artifactId>  
    <version>${mybatis-plus.version}</version>  
</dependency>

<!-- Velocity 模板引擎 -->  
<!-- https://www.cnblogs.com/zhangzhixi/p/15042044.html -->  
<dependency>  
    <groupId>org.apache.velocity</groupId>  
    <artifactId>velocity-engine-core</artifactId>  
    <version>2.3</version>  
</dependency>
```

### 代码生成器 (新)

> [!link]- Reference 
> [MyBatis Plus 代码生成器（最新版本：V3.5.3）使用指南](https://juejin.cn/post/7153855035896512526)
> 
> [MyBatis-plus 代码生成器](https://blog.csdn.net/weixin_44153921/article/details/121437088)

#### Maven

```xml
<!-- MyBatis-Plus 代码生成器 -->  
<dependency>  
    <groupId>com.baomidou</groupId>  
    <artifactId>mybatis-plus-generator</artifactId>  
    <version>${mybatis-plus.version}</version>  
</dependency>
```

#### 创建 CodeGenerator

```java
package com.example.springboot2311.generator;  

import com.baomidou.mybatisplus.generator.FastAutoGenerator;  
import com.baomidou.mybatisplus.generator.config.OutputFile;  
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;  
import java.util.Collections;  

public class CodeGenerator {  
  
    public static void main(String[] args) {  
        FastAutoGenerator.create("jdbc:mysql://127.0.0.1:3306/database1123?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone=Asia/Shanghai", "root", "12345")  
                // 全局配置  
                .globalConfig(builder -> {  
                    builder.author("HuangYw") // 设置作者  
                            .commentDate("yyyy-MM-dd hh:mm:ss")   //注释日期  
                            .outputDir(System.getProperty("user.dir") + "/src/main/java") // 指定输出目录  
                            .disableOpenDir() //禁止打开输出目录，默认打开  
                    ;  
                })  
                // 包配置  
                .packageConfig(builder -> {  
                    builder.parent("com.example.generator") // 设置父包名  
                            .pathInfo(Collections.singletonMap(OutputFile.xml, System.getProperty("user.dir") + "/src/main/resources/mappers")); // 设置mapperXml生成路径  
                })  
                // 策略配置  
                .strategyConfig(builder -> {  
                    builder.addInclude("user") // 设置需要生成的表名  
                            //.addTablePrefix("sys_") // 设置过滤表前缀  
                            // Entity 策略配置  
                            .entityBuilder()  
                            .enableLombok() //开启 Lombok                            .enableFileOverride() // 覆盖已生成文件  
                            .naming(NamingStrategy.underline_to_camel)  //数据库表映射到实体的命名策略：下划线转驼峰命  
                            .columnNaming(NamingStrategy.underline_to_camel)    //数据库表字段映射到实体的命名策略：下划线转驼峰命  
                            // Mapper 策略配置  
                            .mapperBuilder()  
                            .enableFileOverride() // 覆盖已生成文件  
                            // Service 策略配置  
                            .serviceBuilder()  
                            .enableFileOverride() // 覆盖已生成文件  
                            .formatServiceFileName("%sService") //格式化 service 接口文件名称，%s进行匹配表名，如 UserService                            .formatServiceImplFileName("%sServiceImpl") //格式化 service 实现类文件名称，%s进行匹配表名，如 UserServiceImpl                            // Controller 策略配置  
                            .controllerBuilder()  
                            .enableFileOverride() // 覆盖已生成文件  
                    ;  
                })  
                .execute();  
    }  
}
```

### 逻辑删除

步骤1: 配置`com.baomidou.mybatisplus.core.config.GlobalConfig$DbConfig`

application.yml
```properties
mybatis-plus:
  global-config:
    db-config:
      logic-delete-field: flag  # 全局逻辑删除的实体字段名(since 3.3.0,配置后可以忽略不配置步骤2)
      logic-delete-value: 1 # 逻辑已删除值(默认为 1)
      logic-not-delete-value: 0 # 逻辑未删除值(默认为 0)
```

步骤2: 实体类字段上加上 `@TableLogic` 注解
```java
/**
 * 是否显示[0-不显示，1显示]
 */
@TableLogic(value = "1", delval = "0")
private Integer showStatus;
```

> [!link] Reference
> - [MyBatis-Plus 用起来真的很舒服](https://www.cnblogs.com/l-y-h/p/12859477.html)
> - [MybatisX 快速开发插件](https://mp.baomidou.com/guide/mybatisx-idea-plugin.html#%E5%8A%9F%E8%83%BD)
