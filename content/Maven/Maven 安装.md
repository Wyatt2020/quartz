---
created: 2022-05-30 22:48
modified: 2024-04-28 14:00
tags: [Maven]
---

### 下载地址

 [http://maven.apache.org/download.cgi](http://maven.apache.org/download.cgi)

### 历史版本

 [https://archive.apache.org/dist/maven/maven-3/](https://archive.apache.org/dist/maven/maven-3/)

 [Maven 仓库](https://mvnrepository.com/artifact/commons-codec/commons-codec/1.14)

### 环境变量

将 Maven 解压的 `bin` 路径保存到环境变量 `path` 里

### 配置文件 [[Maven settings.xml|settings.xml]]

#### 配置本地仓库地址

`localRepository` 节点

![](202205302257683.png)

#### 配置阿里云仓库

> [!link] 
> [国内 Maven 仓库镜像地址](https://blog.csdn.net/qq_38217990/article/details/129257106)

`mirrors` 节点

```xml
    <!-- 阿里云仓库 -->
    <mirror>
         <id>alimaven</id>
         <mirrorOf>central</mirrorOf>
         <name>aliyun maven</name>
         <url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
    </mirror>

    <!-- 中央仓库 -->
    <mirror>
        <id>repo1</id>
        <mirrorOf>repo1</mirrorOf>
        <name>maven repo1</name>
        <url>https://repo1.maven.org/maven2/</url>
    </mirror>
```

#### 配置 [[JDK 安装|JDK]] 版本

`profiles` 节点

```xml
<profile>
    <id>jdk-1.8</id>
    <activation>
        <activeByDefault>true</activeByDefault>
        <jdk>1.8</jdk>
    </activation>
    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
    </properties>
</profile>
```

### IDEA 配置 Maven

![](202205302303576.png)
