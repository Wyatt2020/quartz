---
created: 2022-06-08 10:45
tags:
  - Spring/注解
---

### @Autowired

  首先根据类型找到对应的 Bean，如果对应类型的 Bean 不是唯一的，那么它会根据其属性名称 Bean 的名称进行匹配。如果匹配得上，就会使用该 Bean ；如果还无法匹配，就会抛出异常。
```java
@Autowired 
private Animal dog; // 注入 Dog ，而不是 Cat
```
  如果不能确定标注属性一定会存在并且允许该属性为 null，那么可以配置属性 `required=false`
```java
@Autowired(required = false)
```
  它除了可以标注属性外，还可以标注方法。可以使用 set 方法从 IoC 容器中找到对应的 Bean 进行注入。
```java
@Autowired 
public void setAnimal(Animal animal) { 
	this.animal = animal; 
}
```

### @Primary

  Animal 有两个实现类（Dog，Cat），Spring 无法通过 `@Autowired` 选择到底注入哪一个。这时候可以使用 `@Primary` 告诉 Spring，当出现选择困难症时，优先选择 `@Primary` 标注的类。
```java
@Primary
@Component
public Cat implements Animal {
    ......
}
```

### @Qualifier

  与 `＠Autowired` 组合使用，通过类型和名称一起找到 Bean。
```java
@Autowired 
@Qualifier("dog")
private Animal animal;
```
  带有参数的构造方法类的装配
```java
@Component
public class BussinessPerson implements Person｛

    private Animal animal = null;

    public BussinessPerson(@Autowired @Qualifier("dog") Animal animal) {
        this.animal = animal;
    }
}
```