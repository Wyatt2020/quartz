---
created: 2023-12-01 13:09
modified: 2023-12-01 13:09
tags:
  - Java
---

### 什么是反射
反射是指在运行状态中，对于任意一个类，都能够知道这个类所有的属性和方法；对于任意一个对象，都能够调用它的任意一个方法；这种动态获取信息以及动态调用对象方法的功能称为反射机制。
例如：
- 加载  `jdbc` 驱动
	- `Class.forName('com.mysql.jdbc.Driver.class');`
- `Spring` 框架的注解

**优点**：可以让代码更加灵活、为各种框架提供开箱即用的功能提供了便利。

**缺点**：让我们在运行时有了分析操作类的能力，这同样也增加了安全问题。比如可以无视泛型参数的安全检查（泛型参数的安全检查发生在编译时）。另外，反射的性能也要稍差点，不过，对于框架来说实际是影响不大的。

### 实现方式

#### 1 . 获取 Class 对象

##### 方法一：类.class

```java
Class clazz = String.class;
```

##### 方法二：实例.getClass()

```java
Class clazz = string.getClass();
```

##### 方法三：全限定类名

```java
Class clazz = Class.forName("java.lang.String");
```

##### 方法四：xxxClassLoader.loadClass()

```java
ClassLoader.getSystemClassLoader().loadClass("cn.javaguide.TargetObject");
```

##### 方式五：基本数据类型的包装类的 TYPE 属性

```java
Class clazz = Integer.TYPE;
```

##### 方式五：使用类加载器加载

### Class 常用方法

```java
Class<?> clazz = Class.forName("com.company.User");
// 获取全限定类名
System.out.println(clazz.getName());
// 获取类名
System.out.println(clazz.getSimpleName());

// 获取类的所有属性
Field[] fields = clazz.getDeclaredFields();
for (Field field: fields) {
    System.out.println(field);
}

// 获取本类和父类的所有方法
Method[] methods = clazz.getMethods();
for (Method method: methods) {
    System.out.println(method);
}

// 获取类的所有方法
Method[] methods2 = clazz.getDeclaredMethods();
for (Method method: methods2) {
    System.out.println(method);
}

// 获取指定的方法
Method method = clazz.getMethod("setName", String.class);
```

- Class 包含类型的 Constructor、Method、Field 等信息
- 获取公开 (public) 元信息，使用 `get{元信息名}s`
- 获取所有的元信息，使用 `getDeclared{元信息名}s`

### 调用构造器创建对象

- 类必须有无参构造器
- 类的构造器访问权限必须足够

```java
Class<?> clazz = Class.forName("com.company.User");

// 直接 new 对象
User user = (User) clazz.newInstance();

// 通过构造器创建对象
Constructor constructor = clazz.getDeclaredConstructor(int.class, String.class, Date.class);
User user2 = (User) constructor.newInstance(1, "1", new Date());

// 通过反射获取普通方法
Method setName = clazz.getMethod("setName", String.class);

// 通过 invoke 调用方法
setName.invoke(user,"马里奥");

// 通过反射获取私有属性
Field name = clazz.getDeclaredField("name");

// 设置 private 属性为可访问
name.setAccessible(true);
name.set(user2, "马里奥");
System.out.println(user2.getName());
```

### 反射获取泛型信息

```java
public class Main {

    public static void main(String[] args) throws NoSuchMethodException {
        Method method = Main.class.getMethod("test", Map.class);
        // 获取 method 的形参类型
        Type[] genericParameterTypes = method.getGenericParameterTypes();
        for (Type genericParameterType : genericParameterTypes) {
            // System.out.println(genericParameterType);
            if (genericParameterType instanceof ParameterizedType) {
                // 返回 genericParameterTypes 的实际类型参数
                Type[] actualTypeArguments = ((ParameterizedType) genericParameterType).getActualTypeArguments();
                for (Type actualTypeArgument : actualTypeArguments) {
                    // System.out.println(actualTypeArgument);
                }
            }
        }
    }

    public void test(Map<String, Integer> map) {}
}
```

### 反射获取注解信息

```java
// 定义@TableAnno
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface TableAnno {
    String value();
}

// 定义@ColumnAnno
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface ColumnAnno {
    String columnName();
    String type();
    int length();
}

@Data
@TableAnno("db_user")
public class User {
    @ColumnAnno(columnName = "age", type = "smallint", length = 3)
    private int age;
    @ColumnAnno(columnName = "name", type = "varchar", length = 20)
    private String name;
    @ColumnAnno(columnName = "birthday", type = "date", length = 10)
    private Date birthday;
}

public class Main {

    public static void main(String[] args) throws ClassNotFoundException {
    
        Class<?> clazz = Class.forName("com.company.User");
        
        // 获取类的注解
        Annotation[] annotations = clazz.getAnnotations();
        for (Annotation annotation : annotations) {
            // System.out.println(annotation);
        }
        
        // 获取属性的注解
        Field[] declaredFields = clazz.getDeclaredFields();
        for (Field declaredField : declaredFields) {
            // 获取注解 columnName 属性的值
            ColumnAnno annotation = declaredField.getAnnotation(ColumnAnno.class);
            // System.out.println(annotation.columnName());
        }

        // 获取指定注解的属性值
        TableAnno tableAnno = clazz.getAnnotation(TableAnno.class);
        String value = tableAnno.value();
        // System.out.println(value);
    }
}
```

> [!link] Reference
> [Java 反射学习总结](https://blog.csdn.net/KingBoyWorld/article/details/105230415)
