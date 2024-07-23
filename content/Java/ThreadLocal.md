---
created: 2022-03-29 09:19
status: doing
tags:
  - Java
---

### 作用

ThreadLocal 的作用主要是做数据隔离，填充的数据只属于当前线程，变量的数据对别的线程而言是相对隔离的，在多线程环境下，防止自己的变量被其它线程篡改。

### 内部实现

![](202203290932277.png)

- ThreadLocal 的静态内部类：ThreadLocalMap。而 Thread 中有个成员变量 threadLocals 可以指向它。
- 可以把 ThreadLocal 看成是一个工具箱，里面提供了一系列操作容器(ThreadLocalMap)的方法：get、set、remove...
- ThreadLocalMap 是默认权限，所以不能直接 new 这个类

### Thread、ThreadLocal、ThreadLocalMap 之间的关系

![](202203291432318.png)

### 源码分析

#### set

1.  线程对象刚创建时，threadLocals 还未赋值，所以是 null
2.  在 ThreadLocal 的 set()中，调用 getMap(currentThread)得到当前线程的 threadLocals。如果发现当前线程尚未绑定 ThreadLocalMap 实例，ThreadLocal 会创建一个 Map 并绑定。此时，Thread 中的 threadLocals 指向新创建的 ThreadLocalMap 实例
3.  ThreadLocalMap 创建的 table 可以看成一个哈希表，默认大小是 16，即有 16 个槽(slot)。创建 table 完毕，根据 firstKey 算出本次插入的槽位，然后用内部类 Entry 将两个值包装成键值对(entry)，放入槽中：`table[i] = new Entry(firstKey, firstValue);`

#### get

1.  ThreadLocalMap 还未初始化：
    - 创建 map
    - 给 map 设置一个键值对{threadLocal : initialValue}
    - 返回 initialValue，默认 null
2.  ThreadLocalMap 已经初始化，但是 map 中没有查到这个 key
    - 往 map 里设置键值对{threadLocal : initialValue}
    - 返回 initialValue，默认 null
3.  推荐创建 ThreadLocal 对象时，复写 initialValue()，减少发生空指针异常

### 应用场景

#### RequestContextHolder

[使用 RequestContextHolder 获取当前请求的 request](RequestContextHolder%20-%20获取当前请求的%20request.md)

> [!link]- Reference
>
> - [浅谈 ThreadLocal](https://zhuanlan.zhihu.com/p/60375306)
> - [再谈谈 ThreadLocal](https://zhuanlan.zhihu.com/p/167937566)
> - [ThreadLocal 项目案例](https://zhuanlan.zhihu.com/p/60665911)
> - [Java 中 ThreadLocal 的实际用途是啥](https://www.zhihu.com/question/341005993)
