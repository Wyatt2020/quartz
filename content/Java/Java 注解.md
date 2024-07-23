---
created: 2023-11-30 21:33
modified: 2023-11-30 21:33
tags:
  - Java
---

### 注解是什么

在 Java 中注解其实就是写在接口、类、属性、方法上的一个标签

### 注解的作用

- 作为特定标记，用于告诉编译器一些信息
- 编译时动态处理，如动态生成代码
- 运行时动态处理，作为额外信息的载体，如获取注解信息

### 注解的分类

- **元注解**：Java 内置的注解，标明该注解的使用范围、生命周期等。
- **标准注解**：Java 提供的基础注解，标明过期的元素 / 标明是复写父类方法的方法 / 标明抑制警告。
- **自定义注解**：第三方定义的注解，含义和功能由第三方来定义和实现。

### 元注解

#### @Retention
标明自定义注解的生命周期

从编写 Java 代码到运行主要周期为 `源文件` → `Class 文件` → `运行时数据`
@Retention 则标注了自定义注解的信息要保留到哪个阶段，分别对应的 `value` 取值(RetentionPolicy)为 `SOURCE` → `CLASS` → `RUNTIME`：
1. *SOURCE*：Java 源代码会保留注解，生成的 class 文件中就没有了
2. *CLASS*：class 文件会保留注解，但是 JVM 加载运行时就没有了
3. *RUNTIME*：运行时会保留注解，**如果想使用反射获取注解信息，则需要使用 RUNTIME**，反射是在运行阶段进行的

##### 各个生命周期的用途

1. *Source*：一个最简单的用法，就是自定义一个注解例如 `@ThreadSafe`，用来标识一个类时线程安全的，就和注释的作用一样，不过更引人注目罢了。
2. *Class*：个人觉得主要是起到标记作用，还没有做实验，例如标记一个 `@Proxy`，JVM 加载时就会生成对应的代理类。
3. *Runtime*：反射实在运行阶段执行的，那么只有 Runtime 的生命周期才会保留到运行阶段，才能被反射读取，也是我们**最常用**的。

#### @Target

描述自定义注解的使用范围，允许自定义注解标注在哪些 Java 元素上 (类、方法、属性、局部属性、参数…)

| 值 (ElementType) | 说明 |
| :--- | --- |
| TYPE | 类、接口、注解、枚举 |
| FIELD | 属性 |
| MEHOD | 方法 |
| PARAMETER | 方法参数 |
| CONSTRUCTOR | 构造函数 |
| LOCAL\_VARIABLE | 局部变量(如循环变量、catch参数) |
| ANNOTATION\_TYPE | 注解 |
| PACKAGE | 包 |
| TYPE\_PARAMETER | 泛型参数 jdk1.8 |
| TYPE\_USE | 任何元素 jdk1.8 |

#### @Inherited

是否可以被标注类的子类继承。
**只有当子类继承父类的时候，注解才会被继承**，类实现接口，或者接口继承接口，都是无法获得父接口上的注解声明的。

#### @Repeatable

是否可以重复标注。这个注解其实是一个语法糖，JDK 1.8 之前也是有办法进行重复标注的，就是使用数组属性。
下面给一个例子，虽然我们标注的是多个 @MyAnnotation，其实会给我们返回一个 @MyAnnotations，相当于是 Java 帮我们把重复的注解放入了一个数组属性中，所以只是一个语法糖而已。

![[Pasted image 20231201114514.png]]


#### @Documented

是否在生成的 JavaDoc 文档中体现，被标注该注解后，生成的 JavaDoc 中，会包含该注解。

### 标准注解

- `@Override` 标记一个方法是覆写父类方法
- `@Deprecated` 标记一个元素为已过期，避免使用
	- `@Target(value={CONSTRUCTOR, FIELD, LOCAL_VARIABLE, METHOD, PACKAGE, PARAMETER, TYPE})`
- `@SuppressWarnings` 不输出对应的编译警告

### 自定义注解

**注解定义格式**
```java
public @interface 注解名 {
  修饰符 返回值 属性名() 默认值;
  修饰符 返回值 属性名() 默认值;
}
```

- 注解的修饰符一般是 `public`
- 定义的类型使用 `@interface`，其实注解就是一个接口，在程序运行时，JVM 会为其生成对应的代理类
- 内部的修饰符只能是 `public`，即使不写也默认是 `public`，因为它本质上就是一个接口，而接口方法的默认访问权限就是 `pubilc`
- 注解的属性本质上是**带默认返回值的接口方法**
- **注解不能继承也不能实现其他类或接口**

返回值支持的类型：
- 基本类型（byte, short, int, long, float, double, boolean, char）
- String
- Class
- Enum
- Annotation
- 以上所有类型的数组类型

### 使用[[Java 反射|反射]]操作注解
Class 对象的相关方法

```java
// 判断该元素是否包含指定注解，包含则返回 true
boolean isAnnotationPresent(Class<? extends Annotation> annotationClass);

// 返回该元素上对应的注解，如果没有返回 null
<T extends Annotation> T getAnnotation(Class<T> annotationClass);

// 返回该元素上的所有注解，如果没有任何注解则返回一个空数组
Annotation[] getAnnotations();

// 返回指定类型的注解，如果没有返回空数组
T[] getAnnotationsByType(Class<T> annotationClass);

// 返回指定类型的注解，如果没有返回空数组，只包含直接标注的注解，不包含inherited的注解
T getDeclaredAnnotation(Class<T> annotationClass);

// 返回指定类型的注解，如果没有返回空数组，只包含直接标注的注解，不包含inherited的注解
T[] getDeclaredAnnotationsByType;

// 返回该元素上的所有注解，如果没有任何注解则返回一个空数组，只包含直接标注的注解，不包含inherited的注解
Annotation[] getDeclaredAnnotations();
```

### 注解的底层实现 - 动态代理

> [!link] Reference
> [Java 注解这一篇就够了](https://blog.csdn.net/KingBoyWorld/article/details/105337011)

### 注解的工作流程
- 通过键值对的形式为注解属性赋值
- 编译器检查注解的使用范围 (将注解信息写入元素属性表 attribute)
- 运行时 JVM 将单个 Class 的 runtime 的所有注解属性取出并最终存入 map 里
- 创建 `AnnotationInvocationHandler` 实例并传入前面的 map
- JVM 使用 JDK 动态代理为注解生成代理类，并初始化处理器
- 调用 `invoke` 方法，通过传入方法名返回注解对应的属性值
