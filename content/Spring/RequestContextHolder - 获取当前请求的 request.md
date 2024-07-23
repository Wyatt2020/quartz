---
created: 2022-06-06 10:13
status: doing 
tags: Spring 
---

`RequestContextHolder` 维护了两个 `ThreadLocal<RequestAttributes>`，它保存了当前线程的 `RequestAttributes` 对象。

SpringMVC 获取 `HttpServletRequest`

```java
HttpServletRequest request = ((ServletRequestAttributes) RequestContextHolder.getRequestAttributes()).getRequest();
```

在登录拦截器里，将用户信息保存到 `HttpServletRequest.Attribute` 里，方便全局获取用户信息。
