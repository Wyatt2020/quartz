---
created: 2023-12-04 14:18
modified: 2024-03-16 17:50
tags: [Java]
---

### 为什么要学 Stream

Stream 能让我们通过 lambda 表达式更简明扼要地以流水线的方式去处理集合内的数据，可以很轻松的完成诸如：过滤、分组、收集、归约这类操作，所以我愿将 Stream 称为函数式接口的最佳实践。

#### Stream 的优点

- 更清晰的代码结构
- 不必关心变量状态：不影响原集合，每次调用都返回一个新的 Stream。
- 延迟执行与优化：Stream 只在遇到终结操作的时候才会执行。比如 filter 方法和 peek 方法都是转换流方法，所以不会触发执行，count 方法是一个终结操作。

#### Stream 的分类

- **转换流**：例如 filter 和 map 方法，将一个 Stream 转换成另一个 Stream，返回值都是 Stream。
- **终结流**：例如 count 和 collect 方法，将一个 Stream 汇总为我们需要的结果，返回值都不是 Stream。

转换流还可以分为

- **无状态**：方法的执行无需依赖前面方法执行的结果集。
- **有状态**：方法的执行需要依赖前面方法执行的结果集。

### 创建流

#### 通过 Stream 接口创建

```java
Stream<Integer> integerStream = Stream.of(1, 2, 3);

Stream<Double> doubleStream = Stream.of(1.1d, 2.2d, 3.3d);

Stream<String> stringStream = Stream.of("1", "2", "3");
```

#### 创建空的 Stream

```java
Stream<Object> empty = Stream.empty();
```

#### 创建无限制元素数量的 Stream

```java
Stream<String> generate = Stream.generate(() -> "Supplier");

Stream<Integer> generateInteger = Stream.generate(() -> 123);
```

#### 通过集合类库进行创建

```java
Stream<Integer> listStream = List.of(1, 2, 3).stream();

Stream<Integer> setStream = Set.of(1, 2, 3).stream();
```

### 创建并行流

```java
List.of(1, 2, 3).parallelStream();

List.of("1", "2", "3").parallelStream();
```

> [!attention] 注：当 Stream 中元素过万甚至更大时，选用并行流才能带来更明显的性能提升。

#### 串行流与并行流的相互转换

```java
//串行流->并行流
Stream.of(1, 2, 3).parallel();

//并行流->串行流
Stream.of(1, 2, 3).parallel().sequential();
```

### 合并 Stream

#### `concat(stream1, stream2)`

```java
Stream.concat(Stream.of(1, 2, 3), Stream.of(4, 5, 6));
```

如果是两种不同的泛型流进行组合，自动推断会自动的推断出两种类型相同的父类

```java
Stream<Integer> integerStream = Stream.of(1, 2, 3);

Stream<String> stringStream = Stream.of("1", "2", "3");

Stream<? extends Serializable> stream = Stream.concat(integerStream, stringStream);
```

### Stream 转换操作之无状态方法

方法的执行无需依赖前面方法执行的结果集。

在 Stream 中无状态的 API 我们常用的大概有以下三个：

#### `map()`

此方法的参数是一个 `Function` 对象，它可以使你对集合中的元素做自定义操作，并保留操作后的元素。

#### `filter()`

此方法的参数是一个 `Predicate` 对象，Predicate 的执行结果是一个 Boolean 类型，所以此方法只保留返回值为 true 的元素，正如其名我们可以使用此方法做一些筛选操作。

#### `flatMap()`

扁平化映射

此方法和 map() 方法一样参数是一个 `Function` 对象，但是此 Function 的返回值要求是一个 Stream，该方法可以将多个 Stream 中的元素聚合在一起进行返回。

Order 对象有一个 `List<Item> ItemList` 属性，下面的代码合并两个 Order 的 ItemList ，结果返回合并后的 Stream

```java
List<Order> orders = List.of(new Order(), new Order());

Stream<Item> itemStream = orders.stream()
        .flatMap(order -> order.getItemList().stream());
```

##### map() 和 flatMap() 的区别

在 Java 中，`map()` 和 `flatMap()` 是 `Stream`、`Optional` 和 `CompletableFuture` 等类中常用的方法，它们都是用来对元素进行转换或处理的。虽然它们的作用类似，但在处理嵌套结构时有一些区别。

`map()` 方法：
- `map()` 方法用于对流中的每个元素进行操作，并将操作后的结果包装成一个新的流返回。
- 如果原始流中的元素是一个对象，经过 `map()` 方法处理后，结果还是一个对象。
- 适用于一对一的转换，不会改变流的结构。

示例：
```java
List<String> list = Arrays.asList("apple", "banana", "cherry");
List<String> result = list.stream()
                          .map(String::toUpperCase)
                          .collect(Collectors.toList());
```

`flatMap()` 方法：
- `flatMap()` 方法用于处理流中的每个元素，并将每个元素转换为一个流，然后将这些流合并为一个新的流。
- 如果原始流中的元素是一个集合或者流，经过 `flatMap()` 方法处理后，结果是一个扁平化的流。
- 适用于一对多的转换，可以处理嵌套的结构。

示例：
```java
List<List<Integer>> list = Arrays.asList(Arrays.asList(1, 2), Arrays.asList(3, 4), Arrays.asList(5, 6));
List<Integer> result = list.stream()
                           .flatMap(Collection::stream)
                           .collect(Collectors.toList());
                           // List<Integer> result = [1, 2, 3, 4, 5, 6]
```

> [!tip] 总结：
> - `map()` 用于一对一的转换，不会改变流的结构。
> - `flatMap()` 用于一对多的转换，可以处理嵌套的结构，并将多个流合并为一个新的流。

#### 不常用方法 peek()

可以通过它做些打印元素之类的操作，API 文档上面也注明了此方法是用于 Debug

```java
List.of(1, 2, 3).stream()
                .map(i -> i * 10)
                .peek(System.out::println)
                .collect(toList());
```

#### 基础类型 Stream

在 Stream 的无状态方法中还有几个和 `map()` 与 `flatMap()` 对应的方法，它们分别是：

- `mapToInt` 返回值为 `IntStream`

- `mapToLong` 返回值为 `LongStream`

- `mapToDouble` 返回值为 `DoubleStream`

- `flatMapToInt` 返回值为 `IntStream`

- `flatMapToLong` 返回值为 `LongStream`

- `flatMapToDouble` 返回值为 `DoubleStream`

其中：

- `IntStream`：对应 基础数据类型中的 `int`、`short`、`char`、`boolean`

- `LongStream`：对应基础数据类型中的 `long`

- `DoubleStream`：对应基础数据类型中的 `double` 和 `float`

这六个方法可以构造 Stream，而且不会自动拆装箱，实际上就是将普通流转换成基础类型流，在我们需要的时候可以拥有更高的效率。

#### 无状态方法的循环合并

举个栗子

```java
List<Integer> list = List.of(1, 2, 3)
				.stream()
                .map(i -> i * 10)
                .filter(i -> i < 10)
                .filter(i -> i % 2 == 0)
                .collect(toList());
```

因为 filter 只依赖 map 的计算结果，而不必依赖 map 执行完后的结果集，所以只要保证先操作 map 再操作 filter，它们就可以在一次循环内完成，这种优化方式被称为循环合并。

所有的无状态方法都可以放在同一个循环内执行，它们也可以使用并行流在多个 CPU 上执行。

### Stream 转换操作之有状态方法

| 方法名 | 方法结果 |
| :- | :- |
| `distinct()` | 元素去重。 |
| `sorted()` | 元素排序，重载的两个方法，需要的时候可以传入一个排序对象。 |
| `limit(long size)` | 传入一个数字，代表只取前 n 个元素。 |
| `skip(long n)` | 传入一个数字，代表跳过 n 个元素，取后面的元素。 |
| `takeWhile(Predicate predicate)` | JDK9 新增，传入一个断言参数当第一次断言为 false 时停止，返回前面断言为 true 的元素。 |
| `dropWhile(Predicate predicate)` | JDK9 新增，传入一个断言参数当第一次断言为 false 时停止，删除前面断言为 true 的元素。 |

以上就是所有的有状态方法，它们的方法执行都必须依赖前面方法执行的结果集才能执行，比如排序方法就需要依赖前面方法的结果集才能进行排序。

同时 `limit` 方法和 `takeWhile` 是两个短路操作方法，这意味效率更高，因为可能内部循环还没有走完时就已经选出了我们想要的元素。

所以有状态的方法不像无状态方法那样可以在一个循环内执行，每个有状态方法都要经历一个单独的内部循环。编写代码时的顺序会影响到程序的执行结果以及性能，在开发过程中要注意。

```java
List<Integer> list = List.of(5, 2, 3, 3, 4, 1);

// [5, 2]
List<Integer> skip = list.stream().skip(0).limit(2).collect(Collectors.toList());  

// [1, 2, 3, 3, 4, 5]
List<Integer> sorted = list.stream().sorted().collect(Collectors.toList());  

// [5, 2, 3, 4, 1]
List<Integer> distinct = list.stream().distinct().collect(Collectors.toList());
```

### 聚合

**聚合方法的特性**

- 代表着整个流计算的最终结果，所以它的返回值都不是 `Stream`。

- 返回值可能为空，比如 `filter` 没有匹配到的情况，JDK8 中用 `Optional` 来规避 `NPE`。

- 都会调用 `evaluate` 方法，这是一个内部方法，看源码的过程中可以用它来判定一个方法是不是聚合方法。

**简单聚合方法**

#### `count()`

- 返回 Stream 中元素的 size 大小。

#### `forEach()`

- 通过内部循环 Stream 中的所有元素，对每一个元素进行消费，此方法没有返回值。

#### `forEachOrder()`

- 和上面方法的效果一样，但是这个可以保持消费顺序，哪怕是在多线程环境下。

#### `anyMatch(Predicate predicate)`

- 这是一个短路操作，通过传入断言参数判断是否有元素能够匹配上断言。

#### `allMatch(Predicate predicate)`

- 这是一个短路操作，通过传入断言参数返回是否所有元素都能匹配上断言。

#### `noneMatch(Predicate predicate)`

- 这是一个短路操作，通过传入断言参数判断是否所有元素都无法匹配上断言，如果是则返回 true，反之则 
false。

#### `findFirst()`

- 这是一个短路操作，返回 Stream 中的第一个元素，Stream 可能为空所以返回值用 Optional 处理。

#### `findAny()`

- 这是一个短路操作，返回 Stream 中的任意一个元素，串型流中一般是第一个元素，Stream 可能为空所以返回值用 Optional 处理。

### 归约

#### `reduce()`

- 所有元素两两之间互相操作

```java
Integer reduce = List.of(1, 2, 3).stream()  
        .reduce((i1, i2) -> i1 + i2).orElse(0); // 6
```

i1 和 i2 就是二元表达式的两个参数，它们分别代表元素中的第一个元素和第二个元素，当第一次相加完成后，所得的结果会赋值到 i1 身上，i2 则会继续代表下一个元素，直至元素耗尽，得到最终结果。

```java
Integer reduce2 = List.of(1, 2, 3).stream() .reduce(0, (i1, i2) -> i1 + i2);
```

在二元表达式前面多加了一个参数，这个参数被称为初始值，这样就算 Stream 没有元素它最终也会返回一个初始值

#### `max()`

根据对比策略返回最大的元素

```java
Optional<MobilePhone> max = createMobilePhone().stream()  
        .max(Comparator.comparing(MobilePhone::getPrice));  
Console.log(max.get()); // iPhone15

public static List<MobilePhone> createMobilePhone() {  
    MobilePhone phone = new MobilePhone("小米10", "小米", new BigDecimal(3999));  
    MobilePhone phone1 = new MobilePhone("小米14", "小米", new BigDecimal(4999));  
    MobilePhone phone2 = new MobilePhone("华为Mate50", "华为", new BigDecimal(6999));  
    MobilePhone phone3 = new MobilePhone("华为Mate60", "华为", new BigDecimal(7999));  
    MobilePhone phone4 = new MobilePhone("iPhone15", "苹果", new BigDecimal(9999));  
    MobilePhone phone5 = new MobilePhone("iPhone14", "苹果", new BigDecimal(8999));  
    return List.of(phone, phone1, phone2, phone3, phone4, phone5);  
}  
  
static class MobilePhone {  
    private String name;  
    private String company;  
    private BigDecimal price;  
  
    public MobilePhone() {  
    }  
    public MobilePhone(String name, String company, BigDecimal price) {  
        this.name = name;  
        this.company = company;  
        this.price = price;  
    }  
  
    public String getName() {  
        return name;  
    }  
  
    public void setName(String name) {  
        this.name = name;  
    }  
  
    public String getCompany() {  
        return company;  
    }  
  
    public void setCompany(String company) {  
        this.company = company;  
    }  
  
    public BigDecimal getPrice() {  
        return price;  
    }  
  
    public void setPrice(BigDecimal price) {  
        this.price = price;  
    }  
  
    @Override  
    public String toString() {  
        return "MobilePhone{" +  
                "name='" + name + '\'' +  
                ", company='" + company + '\'' +  
                ", price=" + price +  
                '}';  
    }  
}
```

#### `min()`

根据对比策略返回最小的元素

```java
Optional<MobilePhone> min = createMobilePhone().stream()  
        .min(Comparator.comparing(MobilePhone::getPrice));  
Console.log(min.get()); // 小米 10
```

### 收集

#### 收集方法

通过 Collectors 我们可以利用它的内置方法很方便的进行数据收集

```java
// toList
List.of(1, 2, 3).stream().collect(Collectors.toList());

// toUnmodifiableList
List.of(1, 2, 3).stream().collect(Collectors.toUnmodifiableList());

// toSet
List.of(1, 2, 3).stream().collect(Collectors.toSet());

// toUnmodifiableSet
List.of(1, 2, 3).stream().collect(Collectors.toUnmodifiableSet());

```

toList 底层也是经典的 ArrayList，toSet 底层则是经典的 HashSet

---

也许有时候你也许想要一个收集成一个 Map，比如通过将订单数据转成一个订单号对应一个订单，那么你可以使用 toMap()：

```java
List<Order> orders = List.of(new Order(), new Order());

Map<String, Order> map = orders.stream()
		.collect(Collectors.toMap(Order::getOrderNo, order -> order));
```

toMap() 具有两个参数：

1. 第一个参数代表 key，它表示你要设置一个 Map 的 key，我这里指定的是元素中的 orderNo。
2. 第二个参数代表 value，它表示你要设置一个 Map 的 value，我这里直接把元素本身当作值，所以结果是一个 `Map<String, Order>`。

你也可以将元素的属性当作值：

```java
List<Order> orders = List.of(new Order(), new Order());

Map<String, List<Item>> map = orders.stream()
		.collect(Collectors.toMap(Order::getOrderNo, Order::getItemList));
```

这样返回的就是一个订单号 + 商品列表的 Map 了。

toMap() 还有两个伴生方法：
1.  `toUnmodifiableMap()`：返回一个不可修改的 Map。
2.  `toConcurrentMap()`：返回一个线程安全的 Map。

这两个方法和 `toMap()` 的参数一模一样，唯一不同的就是底层生成的 Map 特性不太一样，我们一般使用简简单单的 `toMap()` 就够了，它的底层是我们最常用的 `HashMap` 实现。

`toMap()` 功能虽然强大也很常用，但是它却有一个致命缺点。

我们知道 `HahsMap` 遇到相同的 key 会进行覆盖操作，但是 `toMap()` 方法生成 Map 时如果你指定的 key 出现了重复，那么它会直接抛出异常。

比如上面的订单例子中，我们假设两个订单的订单号一样，但是你又将订单号指定了为 key，那么该方法会直接抛出一个 `IllegalStateException`，因为它不允许元素中的 key 是相同的。

#### 分组方法

如果你想对数据进行分类，但是你指定的 key 是可以重复的，那么你应该使用 `groupingBy()` 而不是 `toMap()`。

举个简单的例子，我想对一个订单集合以订单类型进行分组，那么可以这样：

```java
List<Order> orders = List.of(new Order(), new Order());

Map<Integer, List<Order>> collect = orders.stream()
		.collect(Collectors.groupingBy(Order::getOrderType));
```

直接指定用于分组的元素属性，它就会自动按照此属性进行分组，并将分组的结果收集为一个 List。

```java
List<Order> orders = List.of(new Order(), new Order()); 

Map<Integer, Set<Order>> collect = orders.stream() 
		.collect(Collectors.groupingBy(Order::getOrderType, toSet()));
```

`groupingBy` 还提供了一个重载，让你可以自定义收集器类型，所以它的第二个参数是一个 Collector 收集器对象。

对于 Collector 类型，我们一般还是使用 Collectors 类，这里由于我们前面已经使用了 Collectors，所以这里不必声明，直接传入一个 `toSet()` 方法，代表我们将分组后的元素收集为 Set。

groupingBy 还有一个相似的方法叫做 `groupingByConcurrent()`，这个方法可以在并行时提高分组效率，但是它是不保证顺序的。

#### 分区方法

将数据按照 `TRUE` 或者 `FALSE` 进行分组就叫做分区。

举个例子，我们将一个订单集合按照是否支付进行分组，这就是分区：

```java
List<Order> orders = List.of(new Order(), new Order());

Map<Boolean, List<Order>> collect = orders.stream()
		.collect(Collectors.partitioningBy(Order::getIsPaid));        
```

因为订单是否支付只具有两种状态：已支付和未支付，这种分组方式我们就叫做分区。

和 groupingBy 一样，它还具有一个重载方法，用来自定义收集器类型：

```java
List<Order> orders = List.of(new Order(), new Order());

Map<Boolean, Set<Order>> collect = orders.stream()
		.collect(Collectors.partitioningBy(Order::getIsPaid, toSet()));
```

#### 复合方法

就是 Collectors 把 Stream 原先的方法又实现了一遍：
- **map** → `mapping`
- **filter** → `filtering`
- **flatMap** → `flatMapping`
- **count** → `counting`
- **reduce** → `reducing`
- **max** → `maxBy`
- **min** → `minBy`

使用订单类型对订单进行分组，并找出每组有多少个订单。

订单分组我们已经讲过了，找到其每组有多少订单只要拿到对应 list 的 size 就行了，但是我们可以不这么麻烦，而是一步到位，在输出结果的时候键值对就是订单类型和订单数量：

```java
Map<Integer, Long> collect = orders.stream()
		.collect(Collectors.groupingBy(Order::getOrderType, counting()));
```

再举个例子，还是通过订单类型对订单进行分组，但是呢，我们想要拿到每种类型订单金额最大的那个，那么我们就可以这样：

```java
List<Order> orders = List.of(new Order(), new Order());        

Map<Integer, Optional<Order>> collect2 = orders.stream()
		.collect(groupingBy(Order::getOrderType, 
				maxBy(Comparator.comparing(Order::getMoney))));
```

更简洁，也更方便，不需要我们分组完之后再去一一寻找最大值了，可以一步到位。

再来一个分组之后，求各组订单金额之后的：

```java
List<Order> orders = List.of(new Order(), new Order());        

Map<Integer, Long> collect = orders.stream()
		.collect(groupingBy(Order::getOrderType, summingLong(Order::getMoney)));
```

`summingLong()` 求和，支持 Integer、Long 和 Double

`averagingLong()` 求平均，传入 Double

`joining()` 用来拼接字符串：

```java
List<Order> orders = List.of(new Order(), new Order());

String collect = orders.stream()
		.map(Order::getOrderNo).collect(Collectors.joining("，"));
```

> [!link] Reference
> [延迟执行与不可变，系统讲解 Java Stream 数据处理](https://juejin.cn/post/6983835171145383967)
> [归约、分组与分区，深入讲解 Java Stream 终结操作](https://juejin.cn/post/6986805369540444191)
> [Java 8 Stream：2 万字 20 个实例，玩转集合的筛选、归约、分组、聚合](http://www.itsoku.com/article/206#%E7%9B%AE%E5%BD%95)
