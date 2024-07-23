---
created: 2023-12-01 15:18
modified: 2023-12-01 15:18
tags: [Java]
---

```javascript
public void test(Object…objs){}
```

如果你的方法参数像上面那样定义的话，调用就非常的灵活，下面的调用均可

```javascript
test();
test("1");
test("1", "2");
test("1", "2", "3");
```

也就是说，参数可以是任意个

而 `test` 方法里面获取参数时，则可以将 `objs` 当成一个数组

```javascript
if(objs != null && objs.length > 0){
    System.out.println(objs[i]);
}
```

