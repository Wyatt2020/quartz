---
created: 2024-03-29 21:35
modified: 2024-03-29 21:35
tags:
  - Java/JVM
---

### 线程运行诊断

1. CPU 占用过高
	- 用 `top` 定位哪个进程对 CPU 的占用过高
	- 用 `ps` 进一步定位是哪个线程引起的 CPU 占用过高
		- `ps H -eo pid,tid,%cpu | grep 进程id` 
	- 根据线程 id 找到有问题的线程，进一步定位到问题代码的源码行号
		- `jstack 进程id`
2. 程序运行长时间没有响应（死锁）



### 堆内存诊断

**JDK 工具**
1. *jps*：查看当前系统中有哪些 Java 进程
	- 在 cmd 输入 `jps`，查看 Java 程序进程 id
2. *jmap*：查看堆内存占用情况
```bash
jmap -heap 进程id
```

3. *jconsole*：图形界面，多功能的监测工具，可以连续监测
4. *jvisualvm*：图形界面，查看大对象

![[Pasted image 20240329213615.png]]

> [!link] Reference
> [JDK14性能管理工具:Jconsole详解 - 掘金](https://juejin.cn/post/6844903824079978503)
> 
> [深入理解JVM（二十）一一 JVM监控及诊断工具(GUI) - 掘金](https://juejin.cn/post/7012488939219091487#heading-11)
> 
> [【JVM进阶之路】九：性能监控工具-可视化工具篇 - 掘金](https://juejin.cn/post/6949013627290533896#heading-7)
> 
> [Jconsole 开启远程连接遇到的一些坑 - 掘金](https://juejin.cn/post/7246593325935870010#heading-8)

### jvisualvm | VisualVM

> [!download] 
> https://visualvm.github.io/

### jps

显示当前所有 Java 进程 `pid` 的命令，我们可以通过这个命令来查看到底启动了几个 Java 进程（因为每一个 Java 程序都会独占一个 Java 虚拟机实例）。

缺点是只能显示当前用户的进程 id，要显示其他用户的还只能用 Linux 的 `ps` 命令。

```java
jps [options] [hostid]
```

**option参数**：

| 参数   | 说明                        |
| ---- | ------------------------- |
| `-l` | 输出主类全名或 jar 路径            |
| `-q` | 只输出 LVMID                 |
| `-m` | 输出 JVM 启动时传递给 `main()`的参数 |
| `-v` | 输出 JVM 启动时显示指定的 JVM 参数    |

> 其中 `[option]`、`[hostid]` 参数也可以不写。

**如果 jps 命令失效，而我们又要获取 pid，还可以使用以下两种方法：**

```shell
top | grep java
```

```shell
ps -ef |grep java
```

### jstack

*jstack* 是 JDK 自带的一个命令行工具，用于生成 Java 进程的线程转储快照（Thread Dump），以帮助分析 Java 进程中线程的运行状态、堆栈信息等。下面是 jstack 命令的基本使用方法：
1. 打开命令行窗口或终端。
2. 运行以下命令格式来使用 jstack 工具：
   
```shell
jstack [options] pid
```

其中：
- `options` 是 jstack 命令的选项参数，可以选择性地指定，用于配置输出的内容。
- `pid` 是 Java 进程的进程 ID（PID），用于指定要生成线程转储快照的 Java 进程。

常用的 jstack 选项参数包括：
- `-l`：生成详细的线程堆栈信息，包括锁信息。
- `-F`：当 Java 进程没有响应时强制生成线程转储快照。
- `-m`：显示 Java 和 Native C/C++ 混合堆栈信息。
- -h：显示帮助信息，列出 jstack 命令的选项参数说明。

例如，要生成 Java 进程 1234 的线程转储快照，可以运行以下命令：
```shell
jstack -l 1234
```

执行以上命令后，jstack 会输出 Java 进程 1234 中所有线程的堆栈信息，包括每个线程的调用栈信息、锁信息等。通过分析线程转储快照，可以帮助诊断 Java 进程中的线程问题，如死锁、线程阻塞等。

请注意，在使用 jstack 命令生成线程转储快照时，建议在适当的时机执行，避免影响 Java 进程的性能。==通常在需要分析 Java 进程中线程问题时才使用 jstack 命令。==

### jstat

*jstat* 是 JDK 自带的一个命令行工具，它可以监控和获取正在运行的 Java 虚拟机 (JVM) 的性能统计数据，主要用于诊断和调优 JVM 的性能，尤其是在内存管理和垃圾收集方面。

**示例 1: 查看基本的垃圾收集 (GC) 信息**

假设你有一个 Java 进程，其进程 ID (PID) 是 12345，你可以使用 `-gc` 选项来查看与垃圾收集相关的统计信息：

```bash
jstat -gc 12345
```

输出可能类似于：

```log
S0C     S1C     S0U     S1U       EC       EU        OC        OU       MC       MU       CCSC     CCSU      YGC     YGCT       FGC     FGCT     GCT
35648.0 35648.0 0.0     0.0       491520.0 11476.0   1048576.0 106908.0 1048576.0 34816.0  25165824.0 17842176.0 167     0.246   1         0.025   0.271
```

这里的输出列代表了不同的内存区域和 GC 活动的统计，比如 `S0C` 和 `S1C` 分别表示两个 Survivor 区域的容量，`EC` 表示 Eden 区的容量，`YGC` 表示年轻代的垃圾收集次数等。

**示例 2: 实时监控 GC 活动**

如果你想实时监控 GC 活动，可以使用 `-gcutil` 选项，并加上一个时间间隔参数，例如每秒输出一次：

```bash
jstat -gcutil 12345 1000
```

这里 `1000` 表示每 1000 毫秒（1 秒）输出一次 GC 统计信息。

**示例 3: 查看类加载信息**

使用 `-class` 选项可以查看类加载器的统计信息：

```bash
jstat -class 12345
```

输出可能包括已加载的类的数量、已卸载的类的数量以及加载和卸载类所花费的时间等。

**示例 4: 查看编译器统计信息**

使用 `-compiler` 选项可以查看 JIT 编译器的统计信息：

```bash
jstat -compiler 12345
```

这将显示编译器的统计信息，如编译过的代码量和编译时间等。

**示例 5: 查看详细的堆内存信息**

使用 `-gccapacity` 选项可以查看堆内存的详细容量信息：

```bash
jstat -gccapacity 12345
```

这将显示年轻代和老年代的详细内存使用情况，包括最大容量、已使用容量等。

> [!attention] 注意事项
> 在使用 `jstat` 时，确保你有权限访问目标 JVM 的进程。如果 JVM 运行在远程主机上，你可能需要使用 `jstat` 的远程连接选项，例如通过 `-server` 或 `-client` 模式指定连接方式，以及 `-remote` 选项指定远程主机和端口。

### jmap

*jmap* 是 JDK 自带的一个命令行工具，用于生成 Java 进程的内存转储快照（Heap Dump），以帮助分析 Java 进程的内存使用情况。下面是 jmap 命令的基本使用方法：
1. 打开命令行窗口或终端。
2. 运行以下命令格式来使用 jmap 工具：
```shell
jmap [options] pid
```

其中：
- `options` 是 jmap 命令的选项参数，可以选择性地指定，用于配置生成的内存转储快照的内容。
- `pid` 是 Java 进程的进程 ID（PID），用于指定要生成内存转储快照的 Java 进程。

常用的 jmap 选项参数包括：
- `-heap`：打印 Java 堆的概要信息，包括堆空间的使用情况。
- `-histo`：输出 Java 堆中对象的统计信息，包括各个类的实例数量和占用内存大小。
- `-dump: live,format=b,file=<file>`：生成一个包含活动对象的堆转储文件，可以用于后续分析。
- `-F`：当 Java 进程没有响应时强制生成内存转储快照。
- `-h`：显示帮助信息，列出 jmap 命令的选项参数说明。

例如，要生成 Java 进程 1234 的内存转储快照，可以运行以下命令：
```shell
jmap -heap 1234
```

执行以上命令后，jmap 会输出 Java 进程 1234 的堆空间的概要信息，包括堆空间的使用情况。通过分析内存转储快照，可以了解 Java 进程的内存分配情况，帮助诊断内存泄漏、内存占用过高等问题。

请注意，在使用 jmap 命令生成内存转储快照时，建议在适当的时机执行，避免影响 Java 进程的性能。通常在需要分析 Java 进程的内存使用情况时才使用 jmap 命令。

### Jprofiler | 付费

> [!link] Reference
> [Jprofiler 解析 dump 文件使用详解](https://segmentfault.com/a/1190000021869721)

### MAT | Memory Analyzer Tool

> [!download]
> [Eclipse Memory Analyzer Open Source Project | The Eclipse Foundation](https://eclipse.dev/mat/downloads.php)

> [!link] Reference
> [MAT 工具分析 Dump 文件](https://www.cnblogs.com/rb2010/p/14741674.html)