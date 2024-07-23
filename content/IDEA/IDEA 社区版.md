---
created: 2024-04-28 17:42
modified: 2024-04-28 17:46
tags: [IDEA]
source:
  - https://www.cnblogs.com/skysailstar/p/17736274.html
---

## 优点

社区版有两大显而易见的优点：

- 免费。
    
    相较于商业版高昂的价格，社区版的免费无疑是最大的优点。
    
- 正版。
    
    社区版由于是免费的，可以直接使用正版，省去了破解的麻烦，也规避了破解的所有缺点。

## 缺点

当然了，社区版的缺点也是有的：

- 功能较少。
    
    社区版只提供了最基本的 Java 开发相关功能，商业版中的 **Web 开发**、**数据库管理**、**特色功能**都是没有的。
    
    个人觉得这不是问题，因为 **Web 开发**、**数据库管理**都是有专业免费的工具可以实现的，那些**特色功能**在 Java 开发中也不是必需的。
    
- 性能较弱。
    
    社区版的维护是没有商业版频繁的，所以性能和响应速度这些肯定无法与商业版相比。
    
    在一段时间的使用下来，这点性能和响应速度的减弱是完全可以接受的，而且性能较弱也意味着占用的内存较少，对于 IDEA 这个” 内存大户” 甚至可以归为优点。

## 使用

在了解了社区版的优缺点后，就该介绍社区版的使用了。

首先声明，**社区版的使用逻辑与商业版没有任何区别**。

所以，对于熟悉商业版的开发者来说，社区版是没有学习成本的。

## 下载

IDEA 社区版可以直接去 Jetbrains 官网下载，地址为：

[https://www.jetbrains.com/zh-cn/idea/download/download-thanks.html?platform=windows&code=IIC](https://www.jetbrains.com/zh-cn/idea/download/download-thanks.html?platform=windows&code=IIC)

## 功能

这里只介绍社区版相较于商业版不能直接使用的功能。

社区版自带的功能与商业版一致，就不赘述了。

### yml 文件没有自动提示

安装 `Spring Boot Assistant` 插件

### 设置 Spring Boot 启动参数
1. 「*VM options*」是隐藏项，按 `Alt` + `V` 显示
   ![[Pasted image 20240720182952.png]]

2. 在「*Program arguments*」处填入参数，如：`--server.port=9001`

	![[Pasted image 20240720183001.png]]

	![[Pasted image 20240720183008.png]]

### Lombok

与商业版不同，社区版是没有默认安装 Lombok 插件的，所以不能直接使用 Lombok 的功能，需要先安装插件：

![[IDEA 社区版.png]]

安装插件后，Lombok 的功能与商业版一致，这里就不详细介绍了。

### Docker

与商业版不同，社区版是没有默认安装 Docker 插件的，所以不能直接使用 Docker 的功能，需要先安装插件：

![[IDEA 社区版-1.png]]

如果项目中需要使用 Dockerfile 等 Docker 的功能，可以安装此插件实现。

### Tomcat

对于非 Spring Boot 项目，项目的运行需要依赖服务器，常用的服务器 Tomcat 社区版是没有自带功能的，需要先安装插件：

![[IDEA 社区版-2.png]]

然后进行如下步骤配置 Tomcat：

1.  配置服务。
    
    ![[IDEA 社区版-3.png]]
    
2.  添加 Tomcat。
    
    ![[IDEA 社区版-4.png]]
    
3.  配置 Tomcat。
    
    ![[IDEA 社区版-5.png]]

这样，就可以使用 Tomcat 启动服务了。

### 打 war 包

IDEA 社区版不能像商业版那样可以通过工件打 war 包。

如果需要打 war 包，可以使用 Ant 进行打包，具体步骤如下：

1.  创建 `build.xml`。
    
```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<!-- WARNING: Eclipse auto-generated file.
			  Any modifications will be overwritten.
			  To include a user specific buildfile here, simply create one in the same
			  directory with the processing instruction <?eclipse.ant.import?>
			  as the first entry and export the buildfile again. -->
<project basedir="." default="packWar" name="TARGET">
	<property environment="env"/>
	<property name="debuglevel" value="source,lines,vars"/>
	<property name="target" value="1.8"/>
	<property name="source" value="1.8"/>
	
	<!--自定义部分-->
	
	<property name="build" value="${basedir}/build"/>
	<property name="build.class" value="${basedir}/build/classes"/>
	<property name="src" value="${basedir}/src"/>
	<property name="web" value="${basedir}/WebContent"/>
	<property name="project_jar_path" value="${web}/WEB-INF/lib"/>
	<property name="jre_jar_path" value="D:\Environment\jdk\jdk1.8.0_202\jre\lib"/>  
	<property name="jre_jar_ext_path" value="D:\Environment\jdk\jdk1.8.0_202\jre\lib\ext"/>  
	<property name="tomcat_jar_path" value="D:\Environment\apache-tomcat-8.5.79\lib"/>  
	<path id="project_jar">
		<fileset dir="${project_jar_path}">
			<include name="*.jar"/>
		</fileset>
	</path>
	<path id="jre_jar">
		<fileset dir="${jre_jar_path}">
			<include name="*.jar"/>
		</fileset>
	</path>
	<path id="jre_ext_jar">
		<fileset dir="${jre_jar_ext_path}">
			<include name="*.jar"/>
		</fileset>
	</path>
	<path id="tomcat_jar">
		<fileset dir="${tomcat_jar_path}">
			<include name="*.jar"/>
		</fileset>
	</path>
	<path id="TARGET.classpath">
		<pathelement location="build/classes"/>
		<path refid="project_jar"/>
		<path refid="jre_jar"/>
		<path refid="jre_ext_jar"/>
		<path refid="tomcat_jar"/>
	</path>
	<target name="clean">
		<echo message="--------------------开始清理旧的编译文件---------------------------"/>
		<delete dir="build/classes"/>
		<delete file="${basedir}/build/${ant.project.name}.war"/>
	</target>
	<target name="init">
		<echo message="---------------------开始拷贝资源文件-----------------------------"/>
		<mkdir dir="build/classes"/>
		<copy includeemptydirs="false" todir="build/classes">
			<fileset dir="src">
				<exclude name="**/*.java"/>
			</fileset>
		</copy>
		<copy includeemptydirs="false" todir="build/classes">
			<fileset dir="resources">
				<exclude name="**/*.java"/>
			</fileset>
		</copy>
	</target>
	<target name="build">
		<echo message="-----------------------开始编译JAVA文件-----------------------------"/>
		<echo message="${ant.project.name}: ${ant.file}"/>
		<javac debug="true" debuglevel="${debuglevel}" destdir="build/classes" includeantruntime="false" source="${source}" target="${target}">
			<src path="src"/>
			<src path="resources"/>
			<classpath refid="TARGET.classpath"/>
		</javac>
	</target>
	<target name="packWar">
		<echo message="-----------------------开始构建war包-----------------------------"/>
		<war destfile="${basedir}/build/${ant.project.name}.war" webxml="${web}/WEB-INF/web.xml">
			<fileset dir="${web}"/>
			<classes dir="${build.class}"/>
		</war>
	</target>
</project>
```
    
2.  创建 `build.xml` 后会出现 Ant 的控制窗口。
    
    ![[IDEA 社区版-6.png]]
    
3.  选择 `packWar` ，点击鼠标右键选择**属性**。
    
    ![[IDEA 社区版-7.png]]
    
4.  选择**执行**，在 `Ant 命令行` 中输入命令：`clean init build packWar`
    
    ![[IDEA 社区版-8.png]]
    
    点击**确定**保存配置。
    
5.  需要打 war 包时，双击 `packWar` 即可进行打包。
    
    按照 `build.xml` 的配置：
    
    - 打的 war 包会放在 `build` 目录下。
    - 编译后的文件会放在 `build/classess` 目录下。
    
    如果需要调整，可以修改 `build.xml` 中的配置。
