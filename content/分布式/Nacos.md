---
created: 2024-04-30 14:39
modified: 2024-04-30 14:41
tags: [分布式]
---

> [!book] Nacos
> [Releases](https://github.com/alibaba/nacos/releases)
> 
> [官方文档](https://nacos.io)
> 
> [Spring Cloud Alibaba | 用户指南 | 注册配置中心](https://sca.aliyun.com/docs/2022/user-guide/nacos/overview/)
> 
> [Nacos 配置中心安全问题汇总及解决方案](https://nacos.io/blog/nacos-gvr7dx_awbbpb_sdtk4vkbson424zn/?source=blog_article)
> 
> [FAQ](https://nacos.io/docs/latest/guide/user/faq)

> [!todo]
> - [ ] [鉴权插件](https://nacos.io/docs/latest/plugin/auth-plugin/)
> - [ ] [Linux 搭建 Nacos 集群架构以及持久化](https://juejin.cn/post/7352704880282468379)
> - [ ] [配置加密](https://nacos.io/docs/latest/plugin/config-encryption-plugin/)
> - [ ] [多数据源](https://nacos.io/docs/latest/plugin/datasource-plugin/)
> - [ ] [自定义环境变量](https://nacos.io/docs/latest/plugin/custom-environment-plugin/)
> - [ ] [配置变更通知](https://nacos.io/docs/latest/plugin/config-change-plugin/)

### 1. 什么是 Nacos

Nacos `/nɑ:kəʊs/` 是阿里巴巴开源的一款用于微服务架构中的动态配置管理和服务发现的平台。它提供了包括服务发现、配置管理、健康检查、动态配置推送等核心功能，能够帮助开发者更灵活、高效地管理分布式环境中的服务和配置。

Nacos 的核心特性包括：

1. **服务发现与注册：** Nacos 允许服务实例向其注册，同时为服务消费者提供服务实例的发现机制。这使得服务间能自动发现并建立连接，无需硬编码服务地址，提升了系统的弹性和灵活性。
    
2. **配置管理：** Nacos 提供了一个中心化的配置服务，支持实时更新配置并推送给所有相关服务实例，实现了配置的统一管理和动态刷新，这对于微服务架构中的配置一致性管理至关重要。
    
3. **健康监测：** Nacos 监控注册在其上的服务实例的健康状态，当检测到实例不可用时，可以自动从服务列表中移除，确保服务调用的高可用性。
    
4. **动态配置推送：** 支持配置的热更新，即在不重启服务的情况下，新的配置能够实时推送到各个服务实例，降低了配置变更的成本和风险。

### 2. 安装与使用

> [!link] Reference
> [文档](https://nacos.io/docs/latest/quickstart/quick-start/)
> 
>  [下载](https://github.com/alibaba/nacos/releases)

#### 2.1 修改配置

> 在 2.2.0.1 和 2.2.1 版本时，必须执行此变更，否则无法启动；其他版本为建议设置。

修改`conf`目录下的`application.properties`文件。

设置其中的`nacos.core.auth.plugin.nacos.token.secret.key`值，详情可查看[鉴权-自定义密钥](https://nacos.io/docs/latest/plugin/auth-plugin/).

> 注意，文档中的默认值`SecretKey012345678901234567890123456789012345678901234567890123456789`和`VGhpc0lzTXlDdXN0b21TZWNyZXRLZXkwMTIzNDU2Nzg=`为公开默认值，可用于临时测试，实际使用时请**务必**更换为自定义的其他有效值。

#### 2.2 启动服务器

> [!tip]
> Nacos 的运行建议至少在 2C 4G 60G 的机器配置下运行。

**Linux/Unix/Mac**

启动命令(standalone代表着单机模式运行，非集群模式):

```bash
sh startup.sh -m standalone
```

如果您使用的是 ubuntu 系统，或者运行脚本报错提示 ``[[`` 符号找不到，可尝试如下运行：

```bash
bash startup.sh -m standalone
```

**Windows**

```bash
startup.cmd -m standalone
```

http://localhost:8848/nacos

### 3. 三种部署模式

> [!tip]
> Nacos 定义为一个 IDC 内部应用组件，并非面向公网环境的产品，建议在内部隔离网络环境中部署，**强烈不建议部署在公共网络环境**。

**单机模式** - 用于测试和单机试用。
**集群模式** - 用于生产环境，确保高可用。
**多集群模式** - 用于多数据中心场景。

> [!link] Reference
> [Nacos支持三种部署模式](https://nacos.io/docs/latest/guide/admin/deployment/)

### 4. Java 工程脚手架

[Cloud Native App Initializer](https://start.aliyun.com/bootstrap.html)

![[screencapture-start-aliyun-bootstrap-html-2024-06-17-19_29_30.png]]


### 5. 快速集成

[[Nacos 2.3.2 融合 Spring Cloud]]

[[Nacos 1.1.4 融合 Spring Cloud]]
