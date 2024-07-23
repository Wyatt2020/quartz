---
created: 2022-05-05 19:29
modified: 2024-04-24 10:05
tags: [Java]
aliases:
  - Stream 中的 iterate、skip 和 limit 方法与 Consumer 接口的组合使用
---

今天看到同事写的非常有意思的代码，乍一看没看懂，到网上学习了一波这几个的用法才搞懂，都是 Java 8 的新特性。

```java
    /**
     * 验证给定的单据是否符合给定的状态
     * @param ids 单据id列表
     * @exception BusinessException : 存在数据状态与给定状态不一致
     */
    private void allMatchState(List<Integer> ids, int billState) {
        listSpiltOpt(ids, index -> {
            List<Integer> subList = ids.stream().skip(index * 200).limit(200).collect(Collectors.toList());
            if (entryMapper.countByIdsAndState(subList, billState) != subList.size()) {
                throw new BusinessException(CommonExceptionEnum.DATA_REFUSE_OPTIONAL);
            }
        });
    }

    /**
     * list 分页操作
     */
    private void listSpiltOpt(List<Integer> ids, Consumer<? super Integer> action) {
        Stream.iterate(0, n -> n + 1).limit(ids.size() / 200 + 1).parallel().forEach(action);
    }
```

接下一段一段来分析：

---
`Stream.iterate(0, n -> n + 1)`
生成无限流，从 0 开始，每次递增 1，结果就是 `0, 1, 2, 3 …`

---
`limit(ids.size() / 200 + 1)`
`limit` 截取流，只保留 ` ids.size() / 200 + 1 ` 个，假设 ` ids.size() / 200 + 1 ` =2，那么结果就是 ` 0, 1 `

---
`forEach(action)`
遍历操作 `0, 1`，具体操作逻辑是 action，从参数列表来看 action 是 `Consumer<? super Integer>` 类型，就是传入一个方法，在 forEach 调用的时候才执行传入的方法。这里把 action 替换成

```java
index -> {
            List<Integer> subList = ids.stream().skip(index * 200).limit(200).collect(Collectors.toList());
            if (entryMapper.countByIdsAndState(subList, billState) != subList.size()) {
                throw new BusinessException(CommonExceptionEnum.DATA_REFUSE_OPTIONAL);
            }
        }
```

就比较好理解了。

---
`skip(index * 200)`
跳过 `index * 200` 个元素，这里的 index 就是 forEach 要遍历的 `0, 1` ,结果就是 0 和 200

---
`skip(index * 200).limit(200)`
组合起来用就是先遍历 `ids.[0-199]` 的元素，再遍历 `ids.[200-399]` 的元素

> [!link]- Reference
> 
> - [Java 8 新特性 Stream 中的 iterate、skip 和 limit 方法以及组合使用](https://blog.csdn.net/weixin_40745445/article/details/122434349)
> - [Java 8 新特性之 Consumer 的用法](https://blog.csdn.net/qq_36893229/article/details/122866794)
