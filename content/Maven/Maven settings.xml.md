---
created: 2023-11-23 08:57
modified: 2023-11-23 12:37
tags: [Maven]
---

> [!link] Reference
> [Maven – Settings Reference](https://maven.apache.org/settings.html) > [Maven 进阶学习指南](https://juejin.cn/post/7248219648054739002?share_token=263d02d0-9fdc-45e1-a415-18acef97e46d)

**全局配置**：`${maven.home}/conf/settings.xml`

**用户配置**：`${user.home}/.m2/settings.xml`

如果两个文件都存在，则其内容将合并，以特定于用户的  `settings.xml`  文件为主。

可以使用以下表达式对的内容  `settings.xml`  进行插值：

1. `${user.home}`  和所有其他系统属性（从 Maven 3.0 开始）
2. `${env.HOME}`  等环境变量

> 在配置文件  `settings.xml`  中定义的属性不能用于插值。

**项目配置**：`pom.xml`

> [!note]
> **配置优先级从高到低**：pom.xml > 本地 settings > 全局 settings
> 
> 如果这些文件同时存在，在应用配置时，会合并它们的内容，如果有重复的配置，优先级高的配置会覆盖优先级低的。

### 仓库

![[Pasted image 20231123124807.png]]

#### （1）本地仓库

当项目在本地编译或运行时，直接加载本地的依赖服务无疑是最快的。默认情况下，不管在 Window 还是 Linux 下，每个用户在自己用户目录下都有一个路径名为 `.m2/repository/` 的仓库目录。

而原始的本地仓库是为空的，因此 Maven 需要知道一个网络上的仓库，在本地仓库不存在时前往下载网络上的仓库，也就是远程仓库。

#### （2）中央仓库

当 Maven 未配置时，会默认请求 Maven 的中央仓库，中央仓库包含了这个世界上绝大多数流行的开源 Java 构件，以及源码、作者信息、SCM,信息、许可证信息等，每个月这里都会接受全世界 Java 程序员大概 1 亿次的访问，它对全世界 Java 开发者的贡献由此可见一斑。

但是由于最常见的例如网络原因等，国外的中央仓库使用起来并不顺利，因此就有了下载地址为国内的中央仓库，也就是镜像仓库。

#### （3）镜像仓库

总结来说，镜像仓库就是将国外的中心仓库复制一份到国内，这样一来下载速度以及访问速度都将很快。

#### （4）私服

一般来说中央仓库或者镜像仓库都能满足我们的需求，但是当我们在公司内合作开发代码时，可能因为系统保密性原因，有一些其他同事开发的外部依赖只希望能够被本公司的人使用，而如果上传到镜像仓库则保密性就不复存在了。因此私服最主要的功能时存储一些公司内部不希望被公开的依赖服务。

### localRepository

`settings.xml` 文件中的 `localRepository` 元素指定了本地仓库的路径。本地仓库是 Maven 用于存储下载的依赖项和构建输出的地方。默认情况下，它位于你的用户目录下的 `.m2` 文件夹中。

默认值：`${user.home}/.m2/repository`

```xml
<localRepository>D:\Program Files\Apache\maven-repository</localRepository>
```

### interactiveMode

默认值 `true`，是否需要和用户交互以获得输入

### offline

用来标识是否以离线模式运行 Maven，当系统不能联网时，可以通过次配置 `true` 来离线运行

默认值 `false`

### pluginGroups

此元素包含元素列表，每个  `pluginGroup`  元素都包含一个 groupId。当使用插件且命令行中未提供 groupId 时，将搜索该列表。此列表自动包含  `org.apache.maven.plugins`  和  `org.codehaus.mojo` 。

```xml
<pluginGroups>
	<pluginGroup>org.eclipse.jetty</pluginGroup>
</pluginGroups>
```

给定上述设置，Maven 命令行可以使用截断的命令执行  `org.eclipse.jetty:jetty-maven-plugin:run` ：

```shell
mvn jetty:run
```

### servers

由 `POM` 的  `repositories`  和  `distributionManagement`  元素定义，用于下载和部署的远程仓库。

当使用 Maven 私服时，某些私服需要配置认证信息，需要在此处填写相应的配置。之所以不写在 `pom.xml` 中是因为一般项目在上传至代码仓库时同样会将 `pom.xml` 上传，而 `setting.xml` 一般位于用户本地，因此相对比较安全。

```xml
<!--配置服务端的一些设置。一些设置如安全证书不应该和pom.xml一起分发。这种类型的信息应该存在于构建服务器上的settings.xml文件中。 -→
<servers>

	<server>
		<id>server001</id>
		<username>my_login</username>
		<password>my_password</password>
		<privateKey>${user.home}/.ssh/id_dsa</privateKey>
		<passphrase>some_passphrase</passphrase>
		<filePermissions>664</filePermissions>
		<directoryPermissions>775</directoryPermissions>
		<configuration></configuration>
	</server>

	<server>
		<id>siteServer</id>
		<privateKey>/path/to/private/key</privateKey>
		<passphrase>optional; leave empty if not used.</passphrase>
	</server>

</servers>
```

**id**：这是 server 的 id（注意不是用户登陆的 id），该 id 与 `distributionManagement` 中 `repository` 元素的 id 相匹配。

**username**、**password**：鉴权用户名和鉴权密码表示服务器认证所需要的登录名和密码。密码加密功能已被添加到 `2.1.0+`。详情请访问 [密码加密页面](https://maven.apache.org/guides/mini/guide-encryption.html)

**privateKey**：鉴权时使用的私钥位置。和前两个元素类似，私钥位置和私钥密码指定了一个私钥的路径（默认是 `${user.home}/.ssh/id_dsa`）以及如果需要的话，一个密语。将来 `passphrase` 和 `password` 元素可能会被提取到外部，但目前它们必须在 `settings.xml` 文件以纯文本的形式声明。

**passphrase**：鉴权时使用的私钥密码

**filePermissions**、**directoryPermissions**：文件和目录被创建时的权限。如果在部署的时候会创建一个仓库文件或者目录，这时候就可以使用权限（permission）。这两个元素合法的值是一个三位数字，其对应了 unix 文件系统的权限，如 664，或者 775。

> [!attention] 注意：如果使用私钥登录服务器，请确保省略 `<password> ` 元素。否则，私钥将被忽略。

### mirrors

> [!link]- Reference
> [Maven – Guide to Mirror Settings](https://maven.apache.org/guides/mini/guide-mirror-settings.html)

使用镜像可以加快依赖项的下载速度。

```xml
<mirrors>

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

</mirrors>
```

其中 `id` 与 `name` 用来标识唯一的仓库，`url` 为镜像仓库地址，`mirrorOf` 用来匹配当请求什么仓库依赖时使用该镜像。

**mirrorOf**：

- `<mirrorOf>*<mirrorOf>`：匹配所有远程仓库。
- `<mirrorOf>external：*<mirrorOf>`：匹配所有远程仓库，使用 localhost 的除外，使用 `file://` 协议的除外。也就是说，匹配所有不在本机上的远程仓库。
- `<mirrorOf>repo1,repo2<mirrorOf>`：匹配仓库 `repo1` 和 `repo2`，使用逗号分隔多个远程仓库。
- `<mirrorOf>*,!repo1<mirrorOf>`：匹配所有远程仓库，`repo1` 除外，使用感叹号将仓库从匹配中排除。

需要注意的是，由于镜像仓库完全屏蔽了被镜像仓库，当镜像仓库不稳定或者停止服务的时候，Maven 仍将无法访问被镜像仓库，因而将无法下载构件。

> [!attention] 注意：
> Maven 读取 mirror 配置是从上往下读取的，因此谨慎配置 `<mirrorOf>*<mirrorOf>`，因为如果第一个镜像仓库配置了如此标志，那么如果该仓库即使不存在对应依赖也不会向下游查询。

### proxies

如果你需要通过代理服务器访问网络，你可以在 `settings.xml` 中配置代理信息。你可以指定代理的主机、端口、用户名和密码（如果需要身份验证）等信息。这对于在公司网络或防火墙后使用 Maven 非常有用。

```xml
<proxies>
    <!--代理元素包含配置代理时需要的信息 -->
    <proxy>
      <!--代理的唯一定义符，用来区分不同的代理元素。 -->
      <id>myproxy</id>
      <!--该代理是否是激活的那个。true则激活代理。当我们声明了一组代理，而某个时候只需要激活一个代理的时候，该元素就可以派上用处。 -->
      <active>true</active>
      <!--代理的协议。 协议://主机名:端口，分隔成离散的元素以方便配置。 -->
      <protocol>http</protocol>
      <!--代理的主机名。协议://主机名:端口，分隔成离散的元素以方便配置。 -->
      <host>proxy.somewhere.com</host>
      <!--代理的端口。协议://主机名:端口，分隔成离散的元素以方便配置。 -->
      <port>8080</port>
      <!--代理的用户名，用户名和密码表示代理服务器认证的登录名和密码。 -->
      <username>proxyuser</username>
      <!--代理的密码，用户名和密码表示代理服务器认证的登录名和密码。 -->
      <password>somepassword</password>
      <!--不该被代理的主机名列表。该列表的分隔符由代理服务器指定；例子中使用了竖线分隔符，使用逗号分隔也很常见。 -->
      <nonProxyHosts>*.google.com|ibiblio.org</nonProxyHosts>
    </proxy>
</proxies>
```

### profiles

根据环境参数来调整**构建配置**的列表。用于定义一组 `profile`。

`seetings. xml` 中的 `profile` 是  `pom.xml`  中  `profile`  元素的**裁剪版本**。

它包含了 `id`、`activation`、`repositories`、`pluginRepositories`  和  `properties`  元素。这里的 profile 元素只包含这五个子元素是因为这里只关心构建系统这个整体（这正是 settings.xml 文件的角色定位），而非单独的项目对象模型设置。

**如果一个  `settings.xml`  中的  `profile`  被激活，它的值会覆盖任何其它定义在  `pom.xml`  中带有相同 id 的  `profile`。**

#### id

全局唯一标识，如果一个  `settings.xml`  中的  `profile`  被激活，它的值会覆盖任何其它定义在  `pom.xml`  中带有相同 id 的  `profile`。

#### activation

通过  `activation`  元素，你可以基于多个条件来激活配置文件，如操作系统、Java 版本、环境变量等。例如，你可以配置一个  `profile`  仅在特定操作系统下生效。

```xml
<activation>
  <!-- profile 激活的标识，默认 false -->
  <activeByDefault>false</activeByDefault>
  <!-- 当匹配的jdk被检测到，profile被激活。例如，1.4激活JDK1.4，1.4.0_2，而!1.4激活所有版本不是以1.4开头的JDK。 -->
  <jdk>1.8</jdk>
  <!--当匹配的操作系统属性被检测到，profile被激活。os元素可以定义一些操作系统相关的属性。 -->
  <os>
    <!--激活profile的操作系统的名字 -->
    <name>Windows XP</name>
    <!--激活profile的操作系统所属家族(如 'windows') -->
    <family>Windows</family>
    <!--激活profile的操作系统体系结构 -->
    <arch>x86</arch>
    <!--激活profile的操作系统版本 -->
    <version>5.1.2600</version>
  </os>
  <!--如果Maven检测到某一个属性（其值可以在POM中通过${name}引用），其拥有对应的name = 值，Profile就会被激活。如果值字段是空的，那么存在属性名称字段就会激活profile，否则按区分大小写方式匹配属性值字段 -->
  <property>
    <!--激活profile的属性的名称 -->
    <name>mavenVersion</name>
    <!--激活profile的属性的值 -->
    <value>2.0.3</value>
  </property>
  <!--提供一个文件名，通过检测该文件的存在或不存在来激活profile。missing检查文件是否存在，如果不存在则激活profile。另一方面，exists则会检查文件是否存在，如果存在则激活profile。 -->
  <file>
    <!--如果指定的文件存在，则激活profile。 -->
    <exists>${basedir}/file2.properties</exists>
    <!--如果指定的文件不存在，则激活profile。 -->
    <missing>${basedir}/file1.properties</missing>
  </file>
</activation>
```

##### JDK 8 配置

```xml
<profiles>

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

</profiles>
```

##### activeByDefault

**单个配置文件**：默认值为  `false`，当设置为  `true`  时，`profile` 将在没有其他配置文件激活时自动应用。

**多个配置文件**：如果有多个配置文件的  `activeByDefault`  属性设置为  `true`，则只有第一个  `activeByDefault`  设置为  `true`  的配置文件将被默认激活。其他配置文件将不会默认激活，除非它们满足其他激活条件。

**覆盖顺序**：如果多个配置文件都设置了  `activeByDefault`  属性为  `true`，则根据它们在  `pom.xml`  文件中的顺序，后面的配置文件将覆盖前面的配置文件的设置。

#### properties

如果 `profile` 被激活的话，可通过 `POM` 访问属性 `${user.install}`

```xml
<!--
  1. env.X: 在一个变量前加上"env."的前缀，会返回一个shell环境变量。例如,"env.PATH"指代了$path环境变量（在Windows上是%PATH%）。
  2. project.x：指代了POM中对应的元素值。例如: <project><version>1.0</version></project>通过${project.version}获得version的值。
  3. settings.x: 指代了settings.xml中对应元素的值。例如：<settings><offline>false</offline></settings>通过 ${settings.offline}获得offline的值。
  4. java System Properties: 所有可通过java.lang.System.getProperties()访问的属性都能在POM中使用该形式访问，例如 ${java.home}。
  5. x: 在<properties/>元素中，或者外部文件中设置，以${someVar}的形式使用。
 -->
<properties>
  <user.install>${user.home}/our-project</user.install>
</properties>
```

#### repositories

定义了一组远程仓库的列表，当该属性对应的 `profile` 被激活时，会使用该远程仓库。

```xml
<repositories>
  <!--包含需要连接到远程仓库的信息 -->
  <repository>
    <!--远程仓库唯一标识 -->
    <id>codehausSnapshots</id>
    <!--远程仓库名称 -->
    <name>Codehaus Snapshots</name>
    <!--如何处理远程仓库里发布版本的下载 -->
    <releases>
      <!--true或者false表示该仓库是否下载某种类型构建（发布版，快照版）。 -->
      <enabled>false</enabled>
      <!--该元素指定更新发生的频率。Maven会比较本地POM和远程POM的时间戳。这里的选项是：always（一直），daily（默认，每日），interval：X（这里X是以分钟为单位的时间间隔），或者never（从不）。 -->
      <updatePolicy>always</updatePolicy>
      <!--当Maven验证构件校验文件失败时该怎么做-ignore（忽略），fail（失败），或者warn（警告）。 -->
      <checksumPolicy>warn</checksumPolicy>
    </releases>
    <!--如何处理远程仓库里快照版本的下载。有了releases和snapshots这两组配置，POM就可以在每个单独的仓库中，为每种类型的构件采取不同的策略。例如，可能有人会决定只为开发目的开启对快照版本下载的支持。参见repositories/repository/releases元素 -->
    <snapshots>
      <enabled />
      <updatePolicy />
      <checksumPolicy />
    </snapshots>
    <!--远程仓库URL，按protocol://hostname/path形式 -->
    <url>http://snapshots.maven.codehaus.org/maven2</url>
    <!--用于定位和排序构件的仓库布局类型-可以是default（默认）或者legacy（遗留）。Maven 2为其仓库提供了一个默认的布局；然而，Maven 1.x有一种不同的布局。我们可以使用该元素指定布局是default（默认）还是legacy（遗留）。 -->
    <layout>default</layout>
  </repository>
</repositories>
```

#### pluginRepositories

`pluginRepositories`  元素用于定义特定配置文件的插件仓库配置。你可以在这里指定特定配置文件使用的远程插件仓库和本地插件仓库的位置。

### activeProfiles

该元素包含了一组  `activeProfile`  元素，每个  `activeProfile`  都含有一个 profile id。任何在  `activeProfile`  中定义的 profile id，不论环境设置如何，其对应的  `profile`  都会被激活。如果没有匹配的  `profile`，则什么都不会发生。

```xml
<activeProfiles>
    <!-- 要激活的profile id -->
    <activeProfile>env-test</activeProfile>
</activeProfiles>
```

### 激活 Proflie 的三种方式

1. 通过 [[Maven settings.xml#activeProfiles|activeProfiles]] 激活
2. 通过 [[Maven settings.xml#activation|activation]] 激活
3. 通过命令行的方式激活

```shell
mvn -P
```

我们可以通过在 `pom.xml` 或 `setting.xml` 中指定不同环境的 `profile`，在编译构建不同的项目时，通过上述的命令行方式激活对应的 `profIle`。例如在开发环境下：

```shell
mvn package -P dev
```

可以打包开环环境下的项目。

### mirrors 与 repositories 的关系

Maven 加载真正起作用的 repository 的步骤：

1. 首先获取 `pom.xml` 中 repository 的集合，然后获取 `setting.xml` 中 mirror 中元素。
2. 如果 repository 的 `id` 和 mirror 的 `mirrorOf` 的值相同，则该 mirror 替代该 repository。
3. 如果 repository 找不到对应的 mirror，则使用其本身。
4. 依此可以得到最终起作用的 repository 集合。可以理解 mirror 是复写了对应 id 的 repository。

mirror 相当于一个拦截器，会拦截被 `mirrorOf` 匹配到的 repository，在匹配到后，会用 mirror 里定义的 url 替换到 repository。

**没有配置 mirror**

![[Pasted image 20231123140216.png]]

**配置了 mirror**

![[Pasted image 20231123140246.png]]
