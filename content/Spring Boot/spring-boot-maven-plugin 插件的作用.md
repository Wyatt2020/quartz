---
created: 2022-01-06 22:16
modified: 2024-07-21 22:17
tags:
  - SpringBoot
---

### 打包成可执行的 JAR 或 WAR 文件

当你运行 `mvn package` 命令时，`spring-boot-maven-plugin` 会将你的 Spring Boot 应用程序打包成一个可执行的 JAR 或 WAR 文件。这意味着你可以使用 `java -jar your-app.jar` 命令直接运行你的应用，而不需要额外的服务器或复杂的部署过程。

### 指定要执行的启动类

当项目中有两个启动类时，需要指定要执行的启动类，如果不指定，启动会报错。

指定启动类有两种情况需要区分：

1. pom 文件继承自 spring-boot-starter-parent

```xml
<properties>
  <start-class>com.xx.xx</start-class>
</properties>
```

2. pom 文件不是继承自 spring-boot-starter-parent

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <version>2.4.1</version>
            <configuration>
                <mainClass>org.example.sentinel.SentinelApplication</mainClass>
            </configuration>
            <executions>
                <execution>
                    <goals>
                        <goal>repackage</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

`repackage`：重新打包现有的 JAR 和 WAR 归档文件，以便可以使用从命令行执行它们。

> [!reference]
> [Maven Plugin](https://docs.spring.io/spring-boot/maven-plugin/index.html)
> - [Goals](https://docs.spring.io/spring-boot/maven-plugin/goals.html)