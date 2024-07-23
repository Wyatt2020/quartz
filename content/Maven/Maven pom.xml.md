---
created: 2023-11-23 14:07
modified: 2023-11-23 14:07
tags:
  - Maven
---

> [!link] Reference
> - [Maven – POM Reference](https://maven.apache.org/pom.html)
> - [Maven 进阶学习指南](https://juejin.cn/post/7248219648054739002?share_token=263d02d0-9fdc-45e1-a415-18acef97e46d#heading-41)

### 基本信息

```xml
<project xmlns = "http://maven.apache.org/POM/4.0.0"
    xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation = "http://maven.apache.org/POM/4.0.0
    http://maven.apache.org/xsd/maven-4.0.0.xsd">
 
    <!-- 模型版本 一般不用更改 -->
    <modelVersion>4.0.0</modelVersion>
    <!-- 公司或者组织的唯一标志，也是打包成jar包路径的依据 -->
    <!-- 例如com.companyname.project-group，maven打包jar包的路径：/com/companyname/project-group -->
    <groupId>com.companyname.project-group</groupId>
 
    <!-- 项目的唯一ID，一个groupId下面可能多个项目，就是靠artifactId来区分的 -->
    <artifactId>project</artifactId>
 
    <!-- 项目当前版本，格式为:主版本.次版本.增量版本-限定版本号 -->
    <version>1.0</version>
 
    <!--项目产生的构件类型，
    jar、war主要用来标识项目打包出的服务是jar包还是war包 
    pom一般用作多moudle的项目中 顶层的pom用来指定子moudle中需要依赖的版本 详见2.1.3 -->
    <packaging>jar</packaging>
    
    <!--定义了本项目的名称与example的网址 -->
    <name>itoken dependencies</name>
    <url>www.funtl.com</url>
</project>
```

### dependencies【重要】

#### （1）dependencies

本元素定义了项目中所需要的相关依赖信息，也是最重要的元素之一。

```xml
<dependencies>
    <dependency>
        <!------------------- 依赖坐标 ------------------->
        <!--依赖项目的坐标三元素：groupId + artifactId + version -->
        <groupId>org.apache.maven</groupId>
        <artifactId>maven-artifact</artifactId>
        <version>3.8.1</version>
 
        <!------------------- 依赖传递 ------------------->
        <!--依赖排除，即告诉maven只依赖指定的项目，不依赖该项目的这些依赖。此元素主要用于解决版本冲突问题 -->
        <exclusions>
            <exclusion>
                <artifactId>spring-core</artifactId>
                <groupId>org.springframework</groupId>
            </exclusion>
        </exclusions>
        <!-- 可选依赖，用于阻断依赖的传递性。如果在项目B中把C依赖声明为可选，那么依赖B的项目中无法使用C依赖 -->
        <optional>true</optional>
        
        <!------------------- 依赖范围 ------------------->
        <!--依赖范围。在项目发布过程中，帮助决定哪些构件被包括进来
            - compile：默认范围，适用于所有阶段，会随着项目一起发布;  
            - runtime: 在执行时需要使用，如JDBC驱动，适用运行和测试阶段，不同于例如fastjson，需要在编译时使用;   
            - test: 只在测试时使用，用于编译和运行测试代码，例如junit，不同于junit，在发布时并不需要;    
            - optional: 当项目自身被依赖时，标注依赖是否传递。用于连续依赖时使用 -->
        <scope>test</scope>
    </dependency>
</dependencies>
```

**optional**：设置为 `true`  表示该依赖是可选的，项目之间依赖不传递，也不会被打包。不设置 optional（默认）或者 optional 是 false，表示传递依赖。
> [!attention] 注意
> 在 `<dependencyManagement>` 里使用不生效

> [!link] 
> [Maven 中 optional 和 scope 元素的使用 - 知乎](https://zhuanlan.zhihu.com/p/325550529)

#### （2）如何解决依赖传递问题或 jar 包版本冲突问题

解决上述问题一般有两种方式：

- 当我们作为依赖服务提供者时，可以通过 `<optional>` 标签排除掉不想被传递的服务。

```xml
<!-- Project A -->
<project>
  ...
  <dependencies>
    <!-- declare the dependency to be set as optional -->
    <dependency>
      <groupId>sample.ProjectB</groupId>
      <artifactId>Project-B</artifactId>
      <version>1.0</version>
      <optional>true</optional> 
    </dependency>
  </dependencies>
</project>
```

我们的 A 服务中引用了外部的依赖 B 服务，此时有 `A -> B`，在别人引用我们时有 `C -> A ->B`，若此时我们 A 在提供对外服务时不想让别人依赖 B 服务，可以在引用 B 时添加 `<optional>` 标签为 `true`，这样带 C 依赖于 A 时并不会引入 B。如果 C 在此时想要使用 B 的相关服务，需要在 C 的 pom 中显示的调用 B 的相关服务。

- 当我们作为依赖服务使用者时，可以通过 `<exclusions>` 来去除掉我们依赖包中所不想依赖的其他服务。

如果此时我们的 A 服务依赖于 B 服务，B 服务依赖于 C 服务，则有 `A ->B ->C`，因为某种原因例如 jar 包冲突，我们在 A 中并不想依赖于 C 服务的版本，可以在调用 B 服务时去除掉 C 的相关依赖，然后自己再在 A 中使用 C 的相关版本。

```xml
<project>
  ...
  <dependencies>
      
    <dependency>
      <groupId>sample.ProjectB</groupId>
      <artifactId>Project-B</artifactId>
      <version>1.0</version>
      <exclusions>
        <exclusion>
          <!-- 排除掉B中C的相关依赖 -->
          <groupId>sample.ProjectC</groupId>
          <artifactId>Project-C</artifactId>
        </exclusion>
      </exclusions> 
    </dependency>
      
    <!-- 自己引用C的相关版本 -->
    <dependency>
      <groupId>sample.ProjectC</groupId>
      <artifactId>Project-C</artifactId>
      <version>2.0</version>
    </dependency>
      
  </dependencies>
</project>
```

### dependencyManagement

当一个服务中存在有多个 `moudle` 时，每个子 `module` 都可能引用了相同的 jar 包，但是如果将版本维护到子 `module` 的 pom 中，当需要升级时需要修改所有的 pom 文件版本。maven 提供了继承的方式来解决此问题。

```xml
<!--在父pom中定义子pom需要的相关依赖 -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.0.0</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

在父 pom 的 `<dependencyManagement>` 中定义子 pom 需要的依赖及版本。

```xml
<!--在子pom中  如下定义了父pom中相关依赖信息 -->
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>1.5.10.RELEASE</version>
	<relativePath/>
</parent>

<dependencies>
	<dependency>
		<!--因为引用了父pom 所以可以不指定版本 maven会自动去父pom中查找指定版本，此处为1.0.0 -->
		<groupId>org.aspectj</groupId>
		<artifactId>aspectjweaver</artifactId>
	</dependency>
</dependencies>
```

当我们的 pom 中有定义父 pom 的元素后，可以在指定需要的依赖时不指定其版本，maven 会帮我们去父 pom 中查找相关的版本信息。

### properties

properties 主要用来定义常量，通过 `${value}` 来使用。

```xml
 <!--配置依赖版本-->
 <properties>
     <!-- Environment Settings -->
     <java.version>1.8</java.version>
     <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
     <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>

     <!-- Spring cloud Settings   -->
     <spring-cloud.version>Finchley.RELEASE</spring-cloud.version>
     <spring-boot-admin.version>2.0.1</spring-boot-admin.version>
     <zipkin.version>2.10.1</zipkin.version>
 </properties>

<dependencies>
        <!--spring cloud-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        <!--zipkin-->
        <dependency>
            <groupId>io.zipkin.java</groupId>
            <artifactId>zipkin</artifactId>
            <version>${zipkin.version}</version>
        </dependency>
    <dependencies>
```

此外，maven 还通过约定大于配置的方式定义了一些常用的属性。

| 属性 | 定义 |
| --- | --- |
| `${basedir}` | 存放 `pom.xml` 和所有的子目录 |
| `${basedir}/src/main/java` | 项目的 java 源代码 |
| `${basedir}/src/main/resources` | 项目的资源，比如说 property 文件，`springmvc.xml` |
| `${basedir}/src/main/webapp/WEB-INF` | web 应用文件目录，web 项目的信息，比如存放 `web.xml`、本地图片、jsp 视图页面 |
| `${basedir}/target` | 打包输出目录 |
| `${project.version}` | 项目版本 |
| `${project.groupId}` | 项目 groupid |

### resources

`resources` 标签用来标识项目在编译运行时需要额外编译的文件。例如手工引入 jar 包、不同运行环境对应不同的 profile。

```xml
<build>
     <resources>
         <!--首先将默认resources目录下的所有文件包括 -->
         <resource>
             <directory>src/main/resources</directory>
             <filtering>true</filtering>
             <!--只编译所有以.fxml结尾的文件 -->
             <includes>
                 <include>**/*.fxml</include>
             </includes>
             <!--排除掉所有的yaml文件 -->
             <excludes>  
                    <exclude>**/*.yaml</exclude>  
             </excludes>
         </resource>
         <!--将不同环境下对应的不同yaml或properties文件编译运行 -->
         <resource>
             <!--
             <directory>src/main/profiles/dev</directory>
             <directory>src/main/profiles/beta<directory>
             <directory>src/main/profiles/pre</directory>
             -->
             <directory>src/main/profiles/product</directory>
             <filtering>true</filtering>
             <includes>
                 <include>**/*.fxml</include>
             </includes>
         </resource>
         <!--将手工引入的jar包编译运行 -->
         <resource>
                <directory>lib</directory>
                <targetPath>BOOT-INF/lib/</targetPath>
                <includes>
                    <include>**/*.jar</include>
                </includes>
            </resource>
     </resources>
</build>
```

### profile

与 `settings.xml` 中 [[Maven settings.xml#profiles|profile]] 所不同的是，pom 中的 profile 有着更多的标签来描述一组环境。从作用上来区分的话，一般 `setting.xml` 用来标识不同的远程仓库，而 pom 中的 profile 一般用来标识当前项目属于什么环境，如下是一组常见的 pom 中的 profiles。

完整元素如下：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
...
	<profiles>
		<profile>
			<id>test</id>
			<activation>...</activation>
			<build>...</build>
			<modules>...</modules>
			<repositories>...</repositories>
			<pluginRepositories>...</pluginRepositories>
			<dependencies>...</dependencies>
			<reporting>...</reporting>
			<dependencyManagement>...</dependencyManagement>
			<distributionManagement>...</distributionManagement>
		</profile>
	</profiles>
</project>
```

```xml
<profiles>
        <profile>
            <id>dev</id>
            <!--激活条件 其中默认为该 profile -->
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <!--当此 profile 被激活时，会将 project.active 的属性赋值为dev -->
            <properties>
                <project.active>dev</project.active>
            </properties>
        </profile>
        <profile>
            <id>test</id>
            <!--当此 profile 被激活时，会将 project.active 的属性赋值为test -->
            <properties>
                <project.active>test</project.active>
            </properties>
        </profile>
</profiles>

<resources>
         <resource>
             <!-- 根据不同的环境 project.active 取得不同的值，从而会将不同的环境下的yaml或properties文件编译进项目中，达到只需要在编译时指定环境变量即可，不用每次都要修改配置文件 -->
             <directory>src/main/${project.active}</directory>
             <filtering>true</filtering>
             <includes>
                 <include>**/*.fxml</include>
             </includes>
         </resource>
</resources>
```

此外，一般通过 `mvn -P dev/beta/pre/product` 命令来激活不同的环境，也可以通过其他的方式来[[Maven settings.xml#激活 Proflie 的三种方式|激活 profile]]。

当然，pom 中的 profile 不止有指定编译环境的功能，同样也可以指定远程仓库等其他功能。

### modules

当我们项目中有多个模块时，如果我们要单独打包的话需要在每一个模块都执行对应的 maven 命令，但是通过 `<modules>` 标签可以将子服务或平级服务进行聚合，只需要打包该服务，也就会将其对应的子模块同时打包。

```xml
<modules>
    <!-- 引入子模块所在的相对目录 -->
    <module>springmybatis</module>
    <!-- 引入同级模块所在的相对目录 -->
    <module>../utils</module>
 </modules>
```
