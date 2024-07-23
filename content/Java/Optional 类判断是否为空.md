---
created: 2023-12-04 14:50
modified: 2023-12-04 14:50
tags:
  - Java
---

嵌套 `if` 判断值是否为空

```java
Map<String, Object> params = new HashMap<>();
 if (null != request.getData()) {
     if (null != request.getData().getInCode()) {
         params.put("inCode", request.getData().getInCode());
     }
 }

// Optional 的写法
params.put("inCode", Optional.ofNullable()
        .map(RequestData::getInCode).orElse(""));
```

使用 `Optional` 类，优雅判断值是否为空

- `of()` ：使用一个非空的值创建 `Optional` 对象

- `ofNullable()`：接收一个可以为 `null` 的值

- 使用 `map()` 提取对象的值

- `orElse()`：如果为空，则赋默认值

```java
Map<String, Object> params = new HashMap<>();

params.put("inCode", Optional.ofNullable(request.getData())
        .map(RequestData::getInCode).orElse(""));
```

