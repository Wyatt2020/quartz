---
created: 2023-11-23 16:47
modified: 2023-11-23 16:47
tags:
  - Maven
---

> [!link]- Reference
> - [这些 Maven 命令，你会几个](https://cloud.tencent.com/developer/article/1773579)

### 常用

#### 导入本地 jar 包

```shell
mvn install:install-file -Dfile=D:\Cache\QQMsg\979810105\FileRecv\java-sdk-core-3.1.1.jar -DgroupId=com.cloud -DartifactId=apigateway -Dversion=3.1.1 -Dpackaging=jar
```

### maven help

- mvn help:system 显示平台详细信息的列表，例如系统属性和环境变量。
- mvn help:active-profiles 列出了当前对构建 `[有效的配置]` 文件。
- mvn help:all-profiles 列出当前项目下的可用配置文件。
- mvn help:describe：描述插件和 / 或 Mojo（Maven 普通的 Old Java Object）的属性 。
- mvn help:evaluate：以交互方式评估用户给出的 Maven 表达式。
- mvn help:effective-pom：将有效的 POM 显示为当前版本的 XML，并考虑了活动配置文件。如果为 verbose，则会向每个 XML 元素添加一个注释，以描述行的起点。
- mvn help:effective-settings：会在给定配置文件增强功能以及将全局设置继承到用户级别设置的情况下，将计算出的设置显示为项目的 XML。