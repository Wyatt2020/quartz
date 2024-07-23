---
created: 2022-06-02 17:59
modified: 2023-11-18 15:05
tags: [Maven]
source: https://www.cnblogs.com/linyb-geek/p/13181684.html
---

## 什么是依赖冲突

依赖冲突是指项目依赖的某一个 jar 包，有多个不同的版本，因而造成类包版本冲突

## 依赖冲突的原因

依赖冲突很经常是类包之间的间接依赖引起的。每个显式声明的类包都会依赖于一些其它的隐式类包，这些隐式的类包会被 Maven 间接引入进来，从而造成类包冲突

## 如何解决依赖冲突

首先查看产生依赖冲突的类 jar，其次找出我们不想要的依赖类 jar，手工将其排除在外就可以了。具体执行步骤如下

### 1、查看依赖冲突

> a、通过 dependency:tree 是命令来检查版本冲突

```shell
mvn -Dverbose dependency:tree
```

当敲入上述命令时，控制台会出现形如下内容

```
[INFO] org.example:hello:jar:1.0-SNAPSHOT
[INFO] +- org.springframework:spring-context:jar:5.2.7.RELEASE:compile
[INFO] |  +- (org.springframework:spring-aop:jar:5.2.7.RELEASE:compile - omitted for conflict with 5.2.0.RELEASE)
[INFO] |  +- org.springframework:spring-beans:jar:5.2.7.RELEASE:compile
[INFO] |  |  \- (org.springframework:spring-core:jar:5.2.7.RELEASE:compile - omitted for duplicate)
[INFO] |  +- org.springframework:spring-core:jar:5.2.7.RELEASE:compile
[INFO] |  |  \- org.springframework:spring-jcl:jar:5.2.7.RELEASE:compile
[INFO] |  \- org.springframework:spring-expression:jar:5.2.7.RELEASE:compile
[INFO] |     \- (org.springframework:spring-core:jar:5.2.7.RELEASE:compile - omitted for duplicate)
[INFO] \- org.springframework:spring-aop:jar:5.2.0.RELEASE:compile
[INFO]    +- (org.springframework:spring-beans:jar:5.2.0.RELEASE:compile - omitted for conflict with 5.2.7.RELEASE)
[INFO]    \- (org.springframework:spring-core:jar:5.2.0.RELEASE:compile - omitted for conflict with 5.2.7.RELEASE)
```

其中 `omitted for duplicate` 表示有 jar 包被重复依赖，最后写着 `omitted for conflict with xxx` 的，说明和别的 jar 包版本冲突了，而该行的 jar 包不会被引入。比如上面有一行最后写着 `omitted for conflict with 5.2.7.RELEASE`，表示 `spring-core 5.2.0` 版本不会被项目引用，而 `spring-core 5.2.7` 版本会被项目引用

> b、如果是 IDEA，可以安装 **maven helper** 插件来检查依赖冲突

**maven helper** 插件安装成功，点开 `pom.xml` 会发现多了一个 *Dependency Analyzer* 视图，如下  
![Dependency Analyzer.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8yMjY3Nzg3MC0yYjUxM2YwNzRiMDg5MDEwLnBuZw?x-oss-process=image/format,png)  
上面按钮的图标含义如下

- Conflicts（查看冲突）
- All Dependencies as List（列表形式查看所有依赖）
- All Dependencies as Tree（树形式查看所有依赖）

上图说明有 3 个 jar 存在冲突，点击冲突的 jar，可以查看和哪个 jar 产生冲突，如下图  
![查看冲突.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8yMjY3Nzg3MC02NTE3NmRiYWQyN2Q2YzA4LnBuZw?x-oss-process=image/format,png)

### 2、解决冲突

项目的 `pom.xml` 形如下

```xml
<dependencies>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-context</artifactId>
		<version>5.2.7.RELEASE</version>
	</dependency>

	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-aop</artifactId>
		<version>5.2.0.RELEASE</version>
	</dependency>
</dependencies>

```

通过查看依赖树，我们知道项目会引用 `5.2.7.RELEASE` 的 spring core jar 包，而不会引用 `5.2.0.RELEASE` 的 jar 包，如果我们想用 `5.2.0` 版本的 spring core 包，我们该如何做？

> a、使用第一声明者优先原则

谁先定义的就用谁的传递依赖，即在 `pom.xml` 文件自上而下，先声明的 jar 坐标，就先引用该 jar 的传递依赖。因此我们如果要使用 5.2.0 版本的 spring core 包，我们可以改成如下声明

```xml
<dependencies>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-aop</artifactId>
		<version>5.2.0.RELEASE</version>
	</dependency>

	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-context</artifactId>
		<version>5.2.7.RELEASE</version>
	</dependency>
</dependencies>

```

查看依赖树

```
[INFO] org.example:hello:jar:1.0-SNAPSHOT
[INFO] +- org.springframework:spring-aop:jar:5.2.0.RELEASE:compile
[INFO] |  +- org.springframework:spring-beans:jar:5.2.0.RELEASE:compile
[INFO] |  |  \- (org.springframework:spring-core:jar:5.2.0.RELEASE:compile - omitted for duplicate)
[INFO] |  \- org.springframework:spring-core:jar:5.2.0.RELEASE:compile
[INFO] |     \- org.springframework:spring-jcl:jar:5.2.0.RELEASE:compile
[INFO] \- org.springframework:spring-context:jar:5.2.7.RELEASE:compile
[INFO]    +- (org.springframework:spring-aop:jar:5.2.7.RELEASE:compile - omitted for conflict with 5.2.0.RELEASE)
[INFO]    +- (org.springframework:spring-beans:jar:5.2.7.RELEASE:compile - omitted for conflict with 5.2.0.RELEASE)
[INFO]    +- (org.springframework:spring-core:jar:5.2.7.RELEASE:compile - omitted for conflict with 5.2.0.RELEASE)
[INFO]    \- org.springframework:spring-expression:jar:5.2.7.RELEASE:compile
[INFO]       \- (org.springframework:spring-core:jar:5.2.7.RELEASE:compile - omitted for conflict with 5.2.0.RELEASE)

```

通过依赖树，我们可以看到项目已经引入 `5.2.0` 版本的 spring core 包

> b、使用路径近者优先原则

即直接依赖级别高于传递依赖。因此我们可以在最先的 `pom.xml` 添加如下内容

```xml
<dependencies>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-context</artifactId>
		<version>5.2.7.RELEASE</version>
	</dependency>

	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-aop</artifactId>
		<version>5.2.0.RELEASE</version>
	</dependency>

	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-core</artifactId>
		<version>5.2.0.RELEASE</version>
	</dependency>
</dependencies>
```

![路径近者优先.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8yMjY3Nzg3MC1kNzA4Y2UwMmNiMTYwYjkzLnBuZw?x-oss-process=image/format,png)

通过上图可以看到项目引入是 `spring core 5.2.0` 的包

> c、排除依赖

排除依赖如果是 IDEA，可以使用 **maven helper** 插件进行排除。点开 `pom.xml`，切换到 Dependency Analyzer 视图，选择 All Dependencies as Tree，点击要排除的 jar，右键会出现 Execlude 选项，如下  
![去除依赖.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8yMjY3Nzg3MC04MmUxZTg5NmIyNmUyMWIwLnBuZw?x-oss-process=image/format,png)  

它产生的效果和如下配置是一样

```xml
<dependencies>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-context</artifactId>
		<version>5.2.7.RELEASE</version>
		<exclusions>
			<exclusion>
				<artifactId>spring-core</artifactId>
				<groupId>org.springframework</groupId>
			</exclusion>
		</exclusions>
	</dependency>

	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-aop</artifactId>
		<version>5.2.0.RELEASE</version>
	</dependency>
</dependencies>

```

![查看依赖.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8yMjY3Nzg3MC0xNDNhNGIyYTdhYTkxY2I2LnBuZw?x-oss-process=image/format,png)  
通过上图可以看到项目引入是 `spring core 5.2.0` 的包

> d、版本锁定

使用 dependencyManagement 进行版本锁定，dependencyManagement 可以统一管理项目的版本号，确保应用的各个项目的依赖和版本一致。

如果我们项目中只想使用 `spring core 5.2.0` 的包，`pom.xml` 可以改为如下

```xml
<dependencyManagement>
	<dependencies>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
			<version>5.2.0.RELEASE</version>
		</dependency>
	</dependencies>
</dependencyManagement>

<dependencies>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-context</artifactId>
		<version>5.2.7.RELEASE</version>
	</dependency>

	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-aop</artifactId>
		<version>5.2.0.RELEASE</version>
	</dependency>
</dependencies>
```

![版本锁定.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8yMjY3Nzg3MC1hMTQ4NDExZjMyMzgwZDhkLnBuZw?x-oss-process=image/format,png)  

通过上图可以看到项目引入是 `spring core 5.2.0` 的包

## 总结

综上就是 Maven 如何排查依赖冲突以及解决方法，对于排查依赖个人比较推荐使用 **maven helper** 插件，至于解决依赖冲突个人推荐使用**版本锁定**的方法。此外，dependencyManagement 只是声明依赖，并不自动实现引入，因此子项目需要显示的声明需要用的依赖。
