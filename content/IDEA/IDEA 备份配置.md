---
created: 2022-05-31 00:23
modified: 2024-04-28 13:36
tags: [IDEA]
---

## 快速备份

> 备份：File -> Manage IDE Settings -> Export Settings
> 
> 恢复：File -> Manage IDE Settings -> Import Settings

## 自定义备份

在 `安装目录/bin` 找到 `idea.properties`

```properties
#---------------------------------------------------------------------
# 配置目录
#---------------------------------------------------------------------
idea.config.path=D:/Program Files/JetBrains/IntelliJ IDEA 2021.2.3/.IntelliJIdea2021.2/config

#---------------------------------------------------------------------
# 缓存目录
#---------------------------------------------------------------------
idea.system.path=D:/Program Files/JetBrains/IntelliJ IDEA 2021.2.3/.IntelliJIdea2021.2/system

#---------------------------------------------------------------------
# 用户插件目录
#---------------------------------------------------------------------
idea.plugins.path=${idea.config.path}/plugins

#---------------------------------------------------------------------
# 日志目录
#---------------------------------------------------------------------
idea.log.path=${idea.system.path}/log
```

将 `plugins` 和 `.IntelliJIdea2021.2` 打包带走

> [!info] Reference
> [IDEA 配置备份到 GitHub](https://blog.csdn.net/qq_44695727/article/details/106647337)
