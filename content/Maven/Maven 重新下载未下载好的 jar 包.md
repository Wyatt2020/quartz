---
created: 2022-06-02 17:59
modified: 2023-11-18 14:46
tags: [Maven]
source: https://blog.csdn.net/boywcx/article/details/113930992
---

### 前言

首先，如果是个人的项目，相信 Maven 中的 jar 包并不会很多，可以无脑选择删掉仓库，重新再引入依赖下载即可

如果是公司项目，相信 jar 包很多，有时仅仅只是希望重新下载特定未下载好的 jar，那么可以执行下面的步骤流程

### 步骤 1

先到 Maven 仓库所在的目录

![](Maven%20重新下载未下载好的%20jar%20包.png)

在该目录打开 cmd 命令，

![](Maven%20重新下载未下载好的%20jar%20包-1.png)

然后执行

```shell
for /r %i in (*.lastUpdated) do del %i 
```

删除  （带有 `lastUpdated` 的文件都是下载中途出现了异常的，后续 Maven 不会再次主动下载）

![](Maven%20重新下载未下载好的%20jar%20包-2.png)

当步骤 1 走完后，去 IDEA 点击项目右键执行 `Reimport`，看是否解决了问题

![](Maven%20重新下载未下载好的%20jar%20包-3.png)

### 步骤 2：手动定位删除对应的 jar 包

例如我的项目中 `fastjson-1.2.75` 明明已经引入了，可是由于网络问题下载的不完全，进行的报错。

![](Maven%20重新下载未下载好的%20jar%20包-4.png)

`Reimport` 也无效的情况下，需要针对性的到 Maven 仓库中对某一个需要重新下载的 jar 包进行删除

想要快速定位，可以使用 Everything 工具直接定位到具体目录，

![](Maven%20重新下载未下载好的%20jar%20包-5.png)

或者 IDEA 中就可以看到具体引入的 jar 的位置

![](Maven%20重新下载未下载好的%20jar%20包-6.png)

定位到了具体的目录后，进行删除该 jar 的目录

![|550](Maven%20重新下载未下载好的%20jar%20包-7.png)

### 步骤 3

定位到项目的根目录，进入到项目的 `pom.xml` 同级的目录

打开 cmd 执行

```shell
mvn clean install -U
```

然后再次 `Reimport`

如果还是带有红线报警，那么删掉 pom 的依赖，import Maven 一下再 ctrl+z 一下还原依赖，再次 import Maven 红线报警即可消失
