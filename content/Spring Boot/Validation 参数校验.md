---
created: 2023-11-30 16:32
modified: 2023-11-30 16:46
tags:
  - SpringBoot
---

> [!link] Reference
> 
> [Spring Validation 参数效验各种使用姿势](https://juejin.cn/post/7087100869363122189) 
> 
> [Spring Boot 使用 validation 数据校验](https://zhuanlan.zhihu.com/p/444925714)

### Maven

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-validation -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

### 内置的错误提示文件

validationMessages_z

![[Pasted image 20231130163743.png]]

### 使用

1. 在 Controller 方法的参数列表前面添加 `@Valid` 注解

![[Pasted image 20231130164024.png|500]]

2. 在 Entity 要校验的字段添加校验注解

![[Pasted image 20231130164108.png|400]]

3. 在校验的参数后，加上 `BindingResult` 可以获取校验异常的详细信息

![[Pasted image 20231130164150.png]]

### 分组校验

1. 使用 `@Validated` 开启分组校验，并指定分组接口

![[Pasted image 20231130164254.png]]

2. `@Validated` 需要指定分组，新增分组校验接口

![[Pasted image 20240720134327.png]]

3. 给校验注解指定 `groups` 属性，指定注解在何种接口生效

![[Pasted image 20231130164409.png]]

### 自定义校验

1. 编写注解

`@Constraint`：指定校验器

`message`：指定返回的错误提示

`groups`：指定校验分组

`vals`：自定义属性，指定输入的值

```java
@Documented
@Constraint(validatedBy = { ListValueConstraintValidator.class })
@Target({ METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE })
@Retention(RUNTIME)
public @interface ListValue {

    String message() default "{com.study.common.vaild.ListValue.message}";

    Class<?>[] groups() default { };

    Class<? extends Payload>[] payload() default { };

    int[] vals() default { };
}
```

2. 编写校验器

实现 `ConstraintValidator<A extends Annotation, T>` 接口，重写 `initialize`，`isValid` 方法

- `A` 自定义的注解
- `T` 目标参数的类型

```java
public class ListValueConstraintValidator implements ConstraintValidator<ListValue, Integer> {

    private Set<Integer> set = new HashSet<>();

    /**
     * 初始化
     * @param constraintAnnotation 注解输入的值
     */
    @Override
    public void initialize(ListValue constraintAnnotation) {
        int[] vals = constraintAnnotation.vals();
        for (int val: vals) {
            set.add(val);
        }
    }

    /**
     * 判断是否校验成功
     * @param value 需要校验的值
     * @param context
     * @return
     */
    @Override
    public boolean isValid(Integer value, ConstraintValidatorContext context) {
        return set.contains(value);
    }
}
```

3. 编写提示配置文件

`resources/ValidationMessages.properties`

```java
com.study.common.vaild.ListValue.message=必须提交指定的值
```

4. 使用注解

`showStatus` 的值只能是 0 或 1

```java
@ListValue(vals={0, 1}, groups = {AddGroup.class})
private Integer showStatus;
```
