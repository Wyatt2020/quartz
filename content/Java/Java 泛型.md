---
created: 2023-12-01 15:20
modified: 2023-12-01 15:20
tags:
  - Java
---

### 定义

- 泛型的本质就是 "参数化类型"，即给类型指定一个参数，然后在使用时再指定此参数具体的值，那样这个类型就可以在使用时决定了。
- 泛型的出现避免了强制转换的操作，在编译器完成类型转化，也就避免了运行的错误。
### 优点

泛型的好处是在编译的时候检查类型安全，并且所有的强制转换都是自动和隐式的，提高代码的重用率。

**（1）保证了类型的安全性。**
在没有泛型之前，从集合中读取到的每一个对象都必须进行类型转换，如果不小心插入了错误的类型对象，在运行时的转换处理就会出错。
使用了泛型后，编译器在编译期就会做类型检查，告知是否插入了错误类型的对象，使得程序更加安全，增强了程序的健壮性。

**（2） 消除强制转换**
泛型的一个附带好处是，消除源代码中的许多强制类型转换，这使得代码更加可读，并且减少了出错机会。

**（3）避免了不必要的装箱、拆箱操作，提高程序的性能**
在非泛型编程中，将简单类型作为 Object 传递时会引起 Boxing（装箱）和 Unboxing（拆箱）操作，这两个过程都是具有很大开销的。引入泛型后，就不必进行 Boxing 和 Unboxing 操作了，所以运行效率相对较高，特别在对集合操作非常频繁的系统中，这个特点带来的性能提升更加明显。
泛型变量固定了类型，使用的时候就已经知道是值类型还是引用类型，避免了不必要的装箱、拆箱操作。

**（4）提高了代码的重用性。**

### 使用

```java
// 泛型类
public class GenericClass<E> {
    private E name;

    public E getName() {
        return name;
    }

    public void setName(E name) {
        this.name = name;
    }
}

// 泛型方法
public <M> void  method01(M m){
	System.out.println(m);
}

// 泛型接口

public interface GenericInterface<E> {

    public abstract  void add(E e);

    public abstract E getE();
}
```

### `List<?>` 和 `List<Object>` 的区别

1. `List<Object>` 不是 `List<String>` 的父类

2. `List<?>` 是所有 List 泛型的父类，因此 `List<?>` 变量可以被赋值为多种 List 泛型类型

3. `List<?>` 变量的元素只能读，不能写（可以插入 null 元素）

### 通配符

- `<?>` **无界**通配符，可以传任意类型。通常用于泛型方法的调用代码和形参，不能用于定义类和泛型方法

- `<? extends E>` *extends* 声明了类型的**上界**，表示参数的类型必须是 `E` 或者是 `E` 的子类

- `<? super E>` *super* 声明了类型的**下界**，表示参数的类型必须是 `E` 或者是 `E` 的父类

**使用规则**：生产者有上限 `<? extends 生产者>`，消费者有下限 `<? super 消费者>`

1. 如果参数类型表示 T 的生产者，使用 `< ? extends T>`

2. 如果它表示 T 的消费者，就使用 `< ? super T>`

3. 如果既是生产又是消费，那使用通配符就没什么意义了，因为需要是精确的参数类型

4. 频繁 set 的内容，适合用下界通配符 `<? super T>`，因为上界通配符不支持写入

5. 频繁 get 的内容，适合用上界通配符 `<? extends T>`，因为下届通配符会返回 Object，每次转换很麻烦

举个栗子：

```java
// 返回 List 的最大元素
private <E extends Comparable<? super E>> E max(List<? extends E> e1) {

    if (e1 == null){
        return null;
    }
    
    // 迭代器返回的元素属于 E 的某个子类型
    Iterator<? extends E> iterator = e1.iterator();
    E result = iterator.next();
    
    while (iterator.hasNext()){
        E next = iterator.next();
        if (next.compareTo(result) > 0){
            result = next;
        }
    }
    
    return result;
}
```

- 要进行比较，所以 E 需要是可比较的类，因此需要 `extends Comparable<…>`
- `Comparable< ? super E>` 要对 E 进行比较，即 E 的消费者，所以需要用 super 
- 参数 `List< ? extends E>` 表示要操作的数据是 E 的子类的列表，指定上限，这样容器才够大 

### 多重限定

使用 `&` 符号连接多个类型

> [!attention] 注意
> `?` 不能使用多重限定，只有 `T` 可以

```java
public class Client {
    // 工资低于2500元的<上斑族>并且站立的<乘客>车票打8折
    public static <T extends Staff & Passenger> void discount(T t){
        if(t.getSalary()<2500 && t.isStanding()){
            System.out.println("恭喜你！您的车票打八折！");
        }
    }
    public static void main(String[] args) {
        discount(new Me());
    }
}
```

> [!link]- Reference
> [Java 泛型详解](https://cloud.tencent.com/developer/article/2099089)
> 
> [详谈 Java 泛型（参数化类型）](https://blog.csdn.net/qq_45047809/article/details/113656974)
> 
> [深入理解Java泛型(一)](https://mp.weixin.qq.com/s?__biz=Mzg2NDkwMzA0OQ==&mid=2247485782&idx=1&sn=e9eb0f7303c87af2fc34a872400c32cc&cur_album_id=2806853925772591104)

### `<T extends List>` 和 `<? extends List>` 有什么区别

`<T extends List>` 和 `<? extends List>` 都是 Java 泛型中的类型通配符表达式，但它们在使用场景和具体含义上有一定的区别：
1.  `<T extends List>`：
- 这是在声明一个类型参数 T 时使用的，它表示类型 T 是一个 List 或者其子类。
- 当你在定义泛型类或方法时使用这个形式，你可以多次引用 T，并且可以调用 List 接口中所有非修改（只读）的方法。
- 示例：声明一个方法，允许传入任何 List 的子类作为参数，并在方法内部对元素进行操作，但不能添加新元素到列表中。
```java
public <T extends List<String>> void processList(T list) {
   for (String item : list) {
	   // 可以处理item，但不能调用list.add()等添加元素的方法
   }
}
```

2.  `<? extends List>`：
这是一个不确定的具体类型，它也是表示某种 List 或其子类的实例，但是它是用于实际参数化类型的场合，如方法参数、集合初始化等。
使用这种形式通常意味着你只知道传入的是 List 或者它的某个子类，但具体类型未知，同样只能调用非修改方法。
示例：接受一个 List 的任意子类对象，但不知道确切类型。
```java
public void acceptAnyList(List<? extends List<String>> listOfLists) {
   for (List<String> list : listOfLists) {
	   // 可以遍历每个列表并读取元素，但不能向任何一个列表添加元素
   }
}
```

总结来说，两者都是为了限制类型为List或其子类，但在实际应用上：
- `<T extends List>` ==用于定义泛型方法或类==，其中 T 可以在整个方法或类的作用域内被当作已知类型来使用。
- `<? extends List>` 用于传递具体类型的参数时，表明该参数必须是 List 或其子类的一个实例，但它不提供重新绑定变量的机会，只能作为一个匿名的有限制的类型。
	- ==不能用于定义类，只能用于定义方法==