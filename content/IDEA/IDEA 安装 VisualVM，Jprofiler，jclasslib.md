---
created: 2024-03-30 10:31
modified: 2024-03-30 10:33
tags: [IDEA, Java/JVM]
source:
  - https://juejin.cn/post/6997281868362350599
---

## idea 安装 VisualVM

1.  [VisualVM 下载](http://visualvm.github.io/ "http://visualvm.github.io/")
    
2.  配置文件修改：监控的本地 jdk 路径（不修改也是能用，只是不能显示详细的包名和类名，只显示进程号难以区分）

![[IDEA 安装 VisualVM，Jprofiler，jclasslib.webp]]

![[IDEA 安装 VisualVM，Jprofiler，jclasslib-1.webp]]

3.  idea 下载插件

![[IDEA 安装 VisualVM，Jprofiler，jclasslib-2.webp]]

4.  配置下载好的 VisualVM 启动程序路径和本地 jdk 路径

![[IDEA 安装 VisualVM，Jprofiler，jclasslib-3.webp]]

5.  Debug 模式启动即可启动 VisualVM 界面（run 模式不能启动，有解决的小伙伴留下言）

![[IDEA 安装 VisualVM，Jprofiler，jclasslib-4.webp]]

6.  有些 jdk 版本自身就带有 VisualVM 插件。如 OracleJDK8 版本 ，在 bin 目录下就有 jvisualVM.exe

该版本有自带的 jvisualVM:

![[IDEA 安装 VisualVM，Jprofiler，jclasslib-5.webp]]

![[IDEA 安装 VisualVM，Jprofiler，jclasslib-6.webp]]

后期版本都移除了 jdk 自带的 jvisualVM，openJDK 也是没有的，需要单独到官网下载。

## idea 安装 Jprofiler

1.  [Jprofiler 下载地址](https://www.ej-technologies.com/index.html "https://www.ej-technologies.com/index.html")
    
2.  idea 下载插件

![[IDEA 安装 VisualVM，Jprofiler，jclasslib-7.webp]]

3.  配置安装好的 Jprofiler 启动程序路径

![[IDEA 安装 VisualVM，Jprofiler，jclasslib-8.webp]]

4.  Jprofiler 配置集成的是 idea

![[IDEA 安装 VisualVM，Jprofiler，jclasslib-9.webp]]

5.  运行即可监控

![[IDEA 安装 VisualVM，Jprofiler，jclasslib-10.webp]]

## idea 安装 jclasslib 插件查看字节码

1.  下载安装

![[IDEA 安装 VisualVM，Jprofiler，jclasslib-11.webp]]

2.  查看字节码

![[IDEA 安装 VisualVM，Jprofiler，jclasslib-12.webp]]

[idea 下载缓慢的可到官方插件网站下载离线安装](https://plugins.jetbrains.com/ "https://plugins.jetbrains.com/")
