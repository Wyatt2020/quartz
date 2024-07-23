---
created: 2022-05-26 15:53
tags:
  - Spring/注解
---
在少数情况下，bean 不是通过属性或构造函数参数显式依赖于另一个 bean，但却需要要求另一个 bean 优先完成初始化，则可以使用 `@DependsOn` 这个注解。

> [!info]  参考链接
> - [Spring中@DependsOn注解的作用及实现原理解析](https://www.jb51.net/article/207243.htm)

