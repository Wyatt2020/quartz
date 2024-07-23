---
created: 2022-06-02 17:30
modified: 2023-11-18 13:48
tags: [Maven]
---

> [!link] Reference
> [最全 Maven 详解，看这一篇就够啦](https://blog.csdn.net/qq_43410878/article/details/123812267)

### 构建

项目从编译、测试、运行、打包、安装、部署整个过程都交给 Maven 进行管理，这个过程称为构建。

### 仓库分类与查找优先级

本地仓库 > 私服（settings.profile）> pom.profile > 远程仓库（pom. project.repository）和镜像（settings.mirror） > 中央仓库 （settings.mirror.mirrorOf.central）

### Maven 标准目录结构

![|200](Maven%20学习.png)

### 常用 Maven 命令

`mvn clean`：清理已编译文件（删除 target 目录）

`mvn compile`：编译 `src/main/java` 文件，生成 class 文件

`mvn test`：编译 `src/main/test` 文件，自动调用测试脚本

`mvn package`：把 class 文件、配置文件打包成 jar 包或 war 包

`mvn install`：将打包好的文件安装到本地仓库

`mvn deploy`：执行 install，并把包部署到远程仓库

### 生命周期

compile(编译)->test(测试)->package(打包)->install(安装)->deploy(部署)

### POM 标签

modelversion：pom.xml 使用的对象模型版本

parent：父项目

groupId：项目名称，一般写项目的域名

artifactId：模块名称，子项目名或模块名称

version：版本号

packaging：打包类型，一般有 jar、war、pom 等

name：项目的显示名

description：项目描述，常用于 Maven 生成的文档

optional：为 true 表示某个依赖可选，该依赖是否使用都不会影响服务运行，同时不会将依赖传递到依赖该项目的工程

#### scope：作用域

1. **compile**：默认值 ，参与编译、测试、打包、运行
2. **runtime**：不参与编译，参与测试、打包、运行。例如 JDBC 驱动，适用运行和测试阶段
3. **test**：参与编译、测试，不会被打包，例如：junit
4. **provided**：参与编译、测试，不会被打包，也不具有传递性。只有在当 JDK 或者一个容器已提供该依赖之后才使用，比如：servlet api 被 tomcat 容器提供
5. **system**：从参与度来说，和 provided 相同，不过被依赖项不会从 Maven 仓库下载，而是从本地文件系统拿。需要添加 `systemPath` 属性来定义路径。不推荐使用，尽量从 Maven 库中引用依赖。

![[Maven 学习-1.png|425]]

- 当 B 通过 test 或 provided 依赖 A 时，C 不依赖 A
- 当 B 通过 runtime 或 compile 依赖 A 时，C 依赖 A

6. **import**：只能在 `<dependencyManagement>` 里用，继承一个项目的依赖，另外需要指定  ` <type>pom</type> `

```xml
<dependencyManagement>
	 <dependencies>
		<!--继承 springcloud alibaba 的依赖-->
		<dependency>
			<groupId>com.alibaba.cloud</groupId>
			<artifactId>spring-cloud-alibaba-dependencies</artifactId>
			<version>${springcloud.alibaba.version}</version>
			<type>pom</type>
			<scope>import</scope>
		</dependency>
	</dependencies>
</dependencyManagement>
```

### Maven 导入 jar 包中的一些概念

**直接依赖**：项目中直接导入的 jar 包，就是该项目的直接依赖包。
**传递依赖**：项目中没有直接导入的 jar 包，可以通过依赖的直接依赖传递到项目中去。

### 解决 jar 包冲突

#### 方式一

第一声明优先原则。

哪个 jar 包的坐标在靠上的位置，这个 jar 包就是先声明的。

先声明的 jar 包坐标下的依赖包，可以优先进入项目中。

#### 方式二

路径近者优先原则。

直接依赖路径比传递依赖路径近，最终项目进入的 jar 包会是路径近的直接依赖包。

#### 方式三【推荐使用】

直接排除法。使用 exclusions 标签。

当我们要排除某个 jar 包下依赖包，在配置 exclusions 标签的时候，内部可以不写版本号。

因为此时依赖包使用的版本和默认和本 jar 包一样。

![|500](Maven%20学习-2.png)

排除 4.2.4 的 spring-core

Maven 工程是可以分父子依赖关系的。凡是依赖别的项目后，拿到的别的项目的依赖包，都属于传递依赖。

比如：当前 A 项目，被 B 项目依赖。那么我们 A 项目中所有 jar 包都会传递到 B 项目中。B 项目开发者，如果再在 B 项目中导入一套 ssm 框架的 jar 包，对于 B 项目是直接依赖。那么直接依赖的 jar 包就会把我们 A 项目传递过去的 jar 包覆盖掉。

为了防止以上情况的出现。我们可以把 A 项目中主要 jar 包的坐标锁住，那么其他依赖该项目的项目中，即便是有同名 jar 包直接依赖，也无法覆盖。

### 锁定依赖版本 - dependencyManagement

```xml
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

### 统一管理 jar 包版本

```xml
<properties>
      <spring.version>5.0.2.RELEASE</spring.version>
      <slf4j.version>1.6.6</slf4j.version>
      <log4j.version>1.2.12</log4j.version>
      <shiro.version>1.2.3</shiro.version>
      <mysql.version>5.1.6</mysql.version>
      <mybatis.version>3.4.5</mybatis.version>
      <spring.security.version>5.0.1.RELEASE</spring.security.version>
</properties>
```

### 工程拆分与聚合思想

Maven 把一个完整的项目，分成不同的独立模块，这些模块都有各自独立的坐标。哪个地方需要其中某个模块，就直接引用该模块的坐标。

今后如果公司开发一个新项目，我们先考虑问题不是 dao，service，utils，domain 如何编写，要考虑的是这些模块是否已经存在，如果存在直接引用。

以上说的就是 Maven 拆分的思想。把拆分零散的模块聚合到一起编写一个完整的项目，这就是 Maven 聚合思想。

### 父子工程的创建

1.  在父工程下新建子模块
    ![](Maven%20学习-3.png)
2.  父 POM 多了一个 modules 标签
    ![](Maven%20学习-4.png)
3.  子模块 POM 有一个 parent 标签
    ![](Maven%20学习-5.png)

### 工程和模块的区别

- 工程不等于完整的项目，模块也不等于完整的项目。一个完整的项目看的是代码，代码完整，就可以说这是一个完整的项目，和此项目是工程和模块没有关系。
- 工程天生只能使用自己内部资源，工程天生是独立的。后天可以和其他工程或模块建立关联关系。模块天生不是独立的，模块天生是属于父工程的，模块一旦创建，所有父工程的资源都可以使用。
- 父子工程直接，子模块天生集成父工程，可以使用父工程所有资源。子模块之间天生是没有任何关系的。
- 父子工程直接不用建立关系，继承关系是先天的，不需要手动建立。
- 平级直接的引用叫依赖，依赖不是先天的，依赖是需要后天建立的。
- 公共依赖建议放在父工程 POM 文件里；一般情况下，子模块都能继承父工程的依赖。如果没有继承到，那直接在子模块的 POM 文件再导一次依赖。

### 打包部署到私服 - POM 配置

releases - 发行版

snapshots - 测试版

id 要与 setting.xml 的私服配置一样

```xml
<distributionManagement>
        <repository>
            <id>releases</id>
            <url>http://localhost:8081/nexus/content/repositories/releases/</url>
        </repository>
        <snapshotRepository>
            <id>snapshots</id>
            <url>http://localhost:8081/nexus/content/repositories/snapshots/</url>
        </snapshotRepository>
</distributionManagement>
```

### 从私服下载依赖 - POM 配置

```xml
<repositories>
     <repository>
      <id>devops-release</id>
      <name>devops-release</name>
      <url> http://202.100.72.213:8081/repository/devops-release/</url>
      <releases>
         <enabled>true</enabled>
      </releases>
      <snapshots>
         <enabled>true</enabled>
      </snapshots>
   </repository>
</repositories>
```
