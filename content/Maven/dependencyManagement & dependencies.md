---
created: 2022-06-02 17:51
modified: 2023-11-18 14:52
tags: [Maven]
---

```xml
<!-- 父pom -->
<dependencyManagement>
      <dependencies>
        <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-context</artifactId>
          <version>${spring.version}</version>
        </dependency>
      </dependencies>
</dependencyManagement>
```

父 pom 直接引用 `dependencyManagement` 的依赖

子 pom 继承了 `dependencyManagement` 的依赖，但并没有引入，子 pom 想使用，还需要显式的声明依赖。

如果子 pom 不声明版本，默认继承父 pom 的版本；也可以声明自己想用的版本。

```xml
<!-- 子pom -->
<!-- 直接继承父 pom 声明的版本-->
<dependencies>
    <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-context</artifactId>
    </dependency>
</dependencies>

<!-- 使用自定义的版本-->
<dependencies>
    <dependency>
       <groupId>org.aspectj</groupId>
       <artifactId>aspectjrt</artifactId>
       <version>1.7.3</version>
    </dependency>
</dependencies>
```

子 pom 直接继承父 pom 的 `dependencies` 依赖，不需要显式声明
