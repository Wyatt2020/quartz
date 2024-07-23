---
created: 2022-05-05 15:36
modified: 2024-04-24 10:00
tags: [IDEA]
aliases:
  - IDEA debug 时报错：Skipped breakpoint at xx.xxxx.xxx because it happened inside debugger evaluation
---

> IDEA debug 时报错：Skipped breakpoint at xx.xxxx.xxx because it happened inside debugger evaluation

最近 debug 一个项目时，遇到了非常蛋疼的错误。每次以 debug 模式启动该项目，就会停在 URLClassLoader 这个类里，无论点多少次下一步都无法跳出循环。其他项目都可以正常 debug ，唯独这个项目不行。于是我在搜索引擎搜索右下角的提示，但是很多办法都行不通。我试过把一些引入的配置类注释掉，还是不行。
![[IDEA debug 时报错：Skipped breakpoint at xx....png]]

有一篇博客说这是因为打的断点不合适，我取消了全部的断点，还是失败。
![[IDEA debug 时报错：Skipped breakpoint at xx...-1.png]]

然后选择跳过所有断点，可以启动了，但是治标不治本啊。
![[IDEA debug 时报错：Skipped breakpoint at xx...-2.png]]

我对比了一下能正常 debug 的项目的断点配置，发现 Java Exception Breakpoint 配置没有勾选任何选项，于是我取消了该项目勾选的 `java.lang.Exception`，竟然可以成功 debug 了！
![[IDEA debug 时报错：Skipped breakpoint at xx...-3.png]]

这个问题太怪了，我都不清楚什么时候勾选了这个选项，害我找解决方案找了半天🤦‍♂️
