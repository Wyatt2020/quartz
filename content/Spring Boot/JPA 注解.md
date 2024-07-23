---
created: 2023-01-12 16:11
modified: 2023-08-27 23:24
tags:
  - SpringBoot
status: doing
---

### @Entity

将类声明为实体或表

- _name_：指定表名称，默认为当前实体类的非限定名称

### @Table

当实体类与其映射的表名不同名时，需要使用 `@Table` 标注说明，该标注与 `@Entity` 标注并列使用，置于实体类声明语句之前

- _name_：指定数据库的表名
- _catalog_：用于设置表所映射到的数据库的目录
- _schema_：用于设置表所映射到的数据库的模式
- _uniqueConstraints_：设置约束条件

```java
@Entity
@Table(name="t_student")
public class Student{

}
```

### @Basic

用于持久类属性或实例变量上，类型包含 _Java_ 基本类型（byte，short，int，long,float，double,char,boolean），包装类，枚举类，以及实现了 _Serializable_ 接口的类型

可以用在类的属性上以及 _Getter_ 方法上

- _fetch_：指定属性的加载机制
  - EAGER：**默认值**，**即时加载**意味着当实例化对象的时候必须加载该属性值
  - LAZY：**懒加载**是指当实例化对象时不加载该属性，只有当调用该属性时才加载
- _optional_：指定属性是否可空
  - true：**默认值**，可空
  - false：不可为空

如果实体类属性上不加 `@Basic` 注解，它也会自动加上 `@Basic`，并使用默认值

### @Embedded

用于一个实体类要在多个不同的实体类中进行使用，而本身又不需要独立生成一个数据库表，把 `@Embedded` 或 `@Embeddable` 注解类中的字段映射成数据库列嵌入到其他表中

只使用 `@Embedded` 和只使用 `@Embeddable` 产生的效果是相同的

例子：将 A 类嵌入到 B 类里，A 类作为 B 类的表属性

_t_person_

```
id|firstName|lastName|street|city|country|pincode
```

_PersonName.java_

```java
@Data
@Embeddable
public class PersonName {
	private String firstName;
	private String lastName;
}
```

_Address.java_

```java
@Data
@Embeddable
public class Address {
	private String street;
	private String city;
	private String country;
	private String pincode;
}
```

将 _PersonName.java_ 和 _Address.java_ 嵌入到 _Person.java_

```java
@Data
@Entity
public class Person {
	 @Id
	 @GeneratedValue
	 private Integer id;
	 @Embedded
	 private PersonName name;
	 @Embedded
	 private Address address;
}
```

> [!tip] 用法说明
>
> - [JPA @Embeddable 和@Embedded - CSDN 博客](https://blog.csdn.net/dnc8371/article/details/107255427)

### @EmbeddedId

复合 ID

> [!tip] 用法说明
>
> - [Spring JPA @Embedded and @EmbeddedId | Baeldung](https://www.baeldung.com/spring-jpa-embedded-method-parameters)

### @Id

指定表的主键，用于属性的标识

```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```

### @GeneratedValue

与 `@Id` 一同使用，用于标注主键的生成策略

- _IDENTITY_：采用数据库 ID 自增长的方式产生主键，_Oracle_ 不支持这种方式
- _AUTO_： JPA 自动选择合适的策略，是**默认选项**
- _SEQUENCE_：通过序列产生主键，通过 `@SequenceGenerator` 标注指定序列名，_MySQL_ 不支持这种方式
- _TABLE_：通过表产生主键，框架借由表模拟序列产生主键，使用该策略更易于做数据库移植

### @Transient

_JPA_ 会忽略该属性，不会映射到数据库中，即程序运行后数据库中不会有该字段

### @Column

当实体的属性与其映射的列不同名时需要使用 `@Column` 标注说明，可与 `@Id` 标注一起使用

- _name_：指定映射到数据库中的列名
- _unique_：是否唯一，**列级约束**，默认为 false
- _nullable_：是否允许为 null，默认为 true
- _insertable_：在使用 `INSERT` 插入数据时，是否需要插入该字段的值，默认为 true
- _updatetable_：在使用 `UPDATE` 更新数据时，是否需要更新该字段的值，默认为 true
- _columnDefinition_：指定该属性映射到数据库中的实际类型或注释，通常是自动判断。例如：

  - `@Column(columnDefinition="enum('menu', 'button')")`
  - `@Column(name = "remark",columnDefinition="text")`
  - `@Column(name="del_flag",columnDefinition = "varchar(255) comment '删除标志 0:未删除 1:已删除 ")`

- _table_：当映射多个表时，指定表的表中的字段。默认值为主表的表名
- _length_：字段的长度，当字段的类型为 `varchar` 时，该属性才有效，默认为 255 个字符
- _precision_：当字段类型为 `decimal` 时，_precision_ 表示数值的总长度
- _scale_：当字段类型为 `decimal` 时，_scale_ 表示小数点所占的位数

> _insertable_ 和 _updatable_ 属性一般用于只读的属性，例如主键和外键等。这些字段的值通常是自动生成的

### @SequenceGenerator

指定在 `@GeneratedValue` 批注中指定的属性的值，它创建一个序列

### @TableGenerator

指定 `@GeneratedValue` 批注中指定的属性的值生成器。它创建一个用于生成值的表

### @AccessType

此类型的注释用于设置访问类型。如果设置 `@AccessType`（字段），则按字段进行访问。如果设置 `@AccessType`（属性），则访问将发生属性

### @JoinColumn

定义表关联的外键字段名，常与 `@ManyToOne`，`@OneToMany`，`@ManyToMany` 一起使用

`@JoinColumn` 指明了这个实体是关系的拥有者，对应着一个拥有外键的表和它的参照表

- _name_：指定外键的列名称
- _unique_：是否唯一，默认为 false
- _nullable_：是否允许为 null，默认为 true
- _insertable_：是否允许插入，默认为 true
- _updatetable_：是否允许更新，默认为 true
- _columnDefinition_：指定该属性映射到数据库中的实际类型，通常是自动判断
- _referencedColumnName_：指定**目标类的表字段**作为本类的外键，不设置的话默认是目标类的主键

> [!tip] 用法说明
>
> - [JPA 中 @JoinColumn 的 name 和 referencedColumnName 属性怎么辨别 - CSDN 博客](https://blog.csdn.net/Xu_JL1997/article/details/103018249)
> - [@JoinColumn Annotation Explained | Baeldung](https://www.baeldung.com/jpa-join-column)
> - [@JoinColumn 注解 - OpenJPA 教程](https://www.hxstrive.com/subject/open_jpa.htm?id=542)
> - [@JoinColumn 例子 - CSDN 博客](https://blog.csdn.net/u010588262/article/details/76667283)

### @JoinTable

使用 `@JoinTable` 注解将创建一个连接表，也称为“中间表”

`@JoinTable` 一般和 `@ManyToMany` 使用，处理多对多关系，需要两个 Entity 有中间关系表。“一对多”一般不会使用关系表，而选择将“一”直接作为“多”的一个属性

如果缺少 `@JoinTable` 标注，则应用默认值，连接表的名称被假定为使用下划线连接在一起（首先是拥有方）的相关主表的表名

- _name_：指定连接表（中间表）的名称；如果不指定名称，则使用默认表名，即“主表名称\_副表名称”
- _joinColumns_：当前实体在中间表的外键，该外键列引用**拥有关联的实体**的主表
- _inverseJoinColumns_：对方实体在中间表的外键，该外键列引用的是**不拥有关联的实体**的主表

- _uniqueConstraints_：为连接表（中间表）指定一个或多个唯一约束
- _catalog_：可选属性，用于指定数据库实例名，一般来说 `persistence.xml` 文件中必须指定数据库 url，url 中将包含数据库实例
- _schema_：作用与 _catalog_ 属性作用一致

```java
@JoinTable(name = "cust_user",
    joinColumns = @JoinColumn(name = "user_id", referencedColumnName = "id"),
    inverseJoinColumns = @JoinColumn(name = "user_ext_id", referencedColumnName = "id"),
    uniqueConstraints = {
        @UniqueConstraint(name = "unique_user_id", columnNames = {"user_id"}),
        @UniqueConstraint(name = "unique_user_ext_id", columnNames = {"user_ext_id"})
    }
)
```

### @UniqueConstraint

定义**表级约束**或多列约束，配合 `@Table` 使用

- _name_：约束名
- _columnNames_：列名

```java
@Table(uniqueConstraints = {
	@UniqueConstraint(name = "UniqueNumberAndStatus",
		columnNames = {"personNumber", "isActive"}),
	@UniqueConstraint(name = "UniqueSecurityAndDepartment",
		columnNames = {"securityNumber", "departmentCode"})
})
```

> [!tip] 用法说明
>
> - [Defining Unique Constraints in JPA | Baeldung](https://www.baeldung.com/jpa-unique-constraints)

### @ColumnResult

使用 select 子句引用 SQL 查询中的列的名称

### @Enumerated

当实体类中有枚举类型的属性时，默认情况下，自动生成的数据库表中对应的字段类型是枚举的索引值，是数字类型的

若希望数据库中存储的是枚举对应的 `String` 类型，在属性上加入 `@Enumerated(EnumType.STRING)` 即可

```java
@Enumerated(EnumType.STRING)
@Column(nullable = true)
private RoleEnum role;
```

### @Temporal

在 _Java_ 中并没有定义 `Date` 类型的精度(temporal precision)

而在数据库中，表示 `Date` 类型的数据有 `DATE`，`TIME` 和 `TIMESTAMP` 三种精度（即单纯的日期，时间，或者两者兼备）

在进行属性映射时可使用 `@Temporal` 注解来调整精度

```java
@Temporal(TemporalType.DATE)
private Date date;
```

### @Lob

`@Lob` 注解将实体属性/字段映射成数据库支持的大对象类型；当映射到数据库 Lob 类型时，应用程序应该使用 `@Lob` 注释

当元素集合值为基本类型时，`@Lob` 注释可以与 `@Basic` 注释或 `@ElementCollection` 注释结合使用

Lob 可以是二进制或字符类型，Lob 类型是从持久字段或属性的类型推断出来的，除了字符串和基于字符的类型之外，默认为 Blob

`@Lob` 注解支持以下数据库类型的字段：

- _Clob_（ Character Large Ojects） 类型是长字符串类型，`java.sql.Clob`、`Character[]`、`char[]` 和 `String` 将被映射为 _Clob_ 类型。

- _Blob_（ Binary Large Objects） 类型是字节类型，`java.sql.Blob`、`Byte[]`、`byte[]` 和实现了 `Serializable` 接口的类型将被映射为 _Blob_ 类型。

> 注意：Clob、 Blob 占用内存空间较大，一般配合 @Basic(fetch=FetchType.LAZY) 注解将其设置为延迟加载。

```java
@Lob
@Basic(fetch = FetchType.LAZY)
@Column
private byte[] image;
```

> [!tip] 用法说明
>
> - [@Lob 注解 - OpenJPA 教程](https://www.hxstrive.com/subject/open_jpa.htm?id=541)

### @OneToOne

定义联接表之间的一对一关系

- _targetEntity_：指定关联实体类型，默认为被注解的属性或方法所属的类；如果 `Map`，`List`，`Set` 没有使用泛型，则需要手动指定
- _cascade_：定义了当前类对象操作了之后，级联对象的操作

  > [!tip] 级联关系类型
  >
  > - 不定义，则对关系表不会产生任何影响
  >
  > - `CascadeType.REFRESH`：级联刷新，如果父实体被刷新，则其所有相关实体也将被刷新
  >
  > - `CascadeType.REMOVE`：级联删除，如果父实体被移除，则其所有相关实体也将被移除
  >
  > - `CascadeType.MERGE`：级联更新，如果父实体被合并，则其所有相关实体也将被合并
  >
  > - `CascadeType.PERSIST`：级联保存，如果父实体持久存在，则其所有相关实体也将被持久化
  >
  > - `CascadeType.DETACH`：级联分离，如果父实体被分离，那么它的所有相关实体也将被分离；如果要删除一个实体，但是它有外键无法删除，就需要这个级联权限了，它会撤销所有相关的外键关联
  >
  > - `CascadeType.ALL`：级联所有操作

- _fetch_：
  - EAGER：关系类在主类加载的时候同时加载（OneToOne，ManyToOne 的默认值）
  - LAZY：关系类在被访问时才加载（OneToMany，ManyToMany 的默认值）
- _optional_：默认为 true，关联字段是否可空，如果为 false，则常与 `@JoinColumn` 一起使用
- _mappedBy_：该参数只用于关联关系的**被拥有方**，指向拥有方的外键属性名
- _orphanRemoval_：在删除一方数据的同时是否删除掉关联方的数据。默认为 false，不删除关联方数据

> [!tip] mappedBy 的用法
>
> - 单向关系不需要设置该属性，双向关系必须设置，避免双方都建立外键字段
>
> - 关联关系总是被多方(many)维护的，即外键建在多方， 在多方对象使用 `@JoinColumn(name = "外键列名称")`
> - 在单方对象(one)使用 `@OneToMany(mappedby="多方对象的外键属性名")`
>
> - `@JoinColumn` 指明了这个实体是关系的拥有者（对应着一个拥有外键的表和它的参照表）。 `mappedBy` 属性指明在这一边的实体是一个逆向的关系，而拥有者是另一个实体
>
> - 只有 OneToOne，OneToMany，ManyToMany 上才有 mappedBy 属性，ManyToOne 不存在该属性
>
> - mappedBy 标签一定是定义在 the owned side（被拥有方的），他指向 the owning side（拥有方）
>
> - mappedBy 跟 JoinColumn/JoinTable 总是处于互斥的一方

### @OneToMany

定义联接表之间的一对多关系

- _cascade_：定义一方（One）和多方（Many）对象的级联关系，当对一方（One）对象进行了某个操作后，怎样影响到多方（Many）对象的操作
- _orphanRemoval_：在删除一方（One）数据的同时是否删除掉多方（Many）的数据。默认为 false，不删除多方（Many）数据；仅仅将一方（One）和连接表的数据进行删除

例子：一个员工有多个邮箱

> In a One-to-Many/Many-to-One relationship, **the owning side is usually defined on the *many* side of the relationship.** It's usually the side that owns the foreign key.
>
> The *@JoinColumn* annotation defines that actual physical mapping on the owning side:

_Email.java_（the owning side）

```java
@Entity
public class Email {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "employee_id")
    private Employee employee;
}
```

> It simply means that our *Email* entity will have a foreign key column named *employee_id* referring to the primary attribute *id* of our *Employee* entity.

> Once we have defined the owning side of the relationship, Hibernate already has all the information it needs to map that relationship in our database.
>
> To make this association bidirectional, all we'll have to do is to define the referencing side. The inverse or the referencing side simply maps to the owning side.
>
> We can easily use the *mappedBy* attribute of *@OneToMany* annotation to do so.
>
> So, let's define our *Employee* entity:

_Employee.java_（the referencing side）

```java
@Entity
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    @OneToMany(fetch = FetchType.LAZY, mappedBy = "employee")
    private List<Email> emails;
}
```

> Here, **the value of *mappedBy* is the name of the association-mapping attribute on the owning side.** With this, we have now established a bidirectional association between our *Employee* and *Email* entities.

> [!tip] Reference
>
> - [Difference Between @JoinColumn and mappedBy | Baeldung](https://www.baeldung.com/jpa-joincolumn-vs-mappedby)
> - [@OneToMany 注解 - OpenJPA 教程](https://www.hxstrive.com/subject/open_jpa/566.htm)

### @ManyToOne

定义联接表之间的多对一关系

例子：一个老师教多个学生

_Teacher.java_

```java
@Data
@Entity
@Table
public class Teacher {
    @Id
    @GeneratedValue
    private int id;

    @Column
    private String name;

    @OneToMany(mappedBy = "teacher")
    private List<Student> studentList;
}
```

_Student.java_

```java
@Data
@Entity
@Table
public class Student {
    @Id
    @GeneratedValue
    private int id;

    @Column
    private String name;

    @ManyToOne
    @JoinColumn(name = "teacher_id")
    private Teacher teacher;
}
```

> [!tip] Reference
>
> - [@ManyToOne 注解 - OpenJPA 教程](https://www.hxstrive.com/subject/open_jpa/565.htm)

### @ManyToMany

定义联接表之间的多对多关系

例子：一个老师教多个学生，一个学生也有多个科目的老师

```java
public class Teacher {
    //...
    @ManyToMany(mappedBy = "teacherList")
    private List<Student> studentList;
}

public class Student {
    //...
    @ManyToMany
    private List<Teacher> teacherList;
}
```

> [!tip] Reference
>
> - [@ManyToMany 注解 - OpenJPA 教程](https://www.hxstrive.com/subject/open_jpa/567.htm)

### @OrderBy

指定在查询一对多、多对多关联关系中，关联集合元素的顺序

```java
// JPA 默认根据 Student 的 ID 主键对 studentList 集合数据进行递增排序
@OneToMany(cascade = CascadeType.ALL)
@OrderBy
private List<Student> studentList;

// 手动指定 id 字段的排序方式，ASC 递增排序，DESC 递减排序
@OneToMany(cascade = CascadeType.ALL)
@OrderBy("id desc")
private List<Student> studentList;

// 手动指定按照多个属性进行排序
// 下面将根据 sex 和 salay 进行递增排序
@OneToMany(cascade = CascadeType.ALL)
@OrderBy("sex,salary")
private List<Student> studentList;

// 下面将根据 sex 递增排序，salary 递增排序
@OneToMany(cascade = CascadeType.ALL)
@OrderBy("sex asc,salary asc")
private List<Student> studentList;

// 下面将根据 sex 递增排序，salary 递减排序
@OneToMany(cascade = CascadeType.ALL)
@OrderBy("sex asc,salary desc")
private List<Student> studentList;
```

### @NamedQueries

指定命名查询的列表

### @NamedQuery

使用静态名称指定查询

## Spring Data JPA

### @Query

```java
public @interface Query {

	/** 定义被执行的 sql 或者 hql */
	String value() default "";

	/** 分页时用于查询中数量的 sql 或者 hql */
	String countQuery() default "";

	/** 用原生的分页 */
	String countProjection() default "";

	/** 使用原生 sql，为 false 时使用 hql */
	boolean nativeQuery() default false;

	/** 定义该查询的名字 */
	String name() default "";

	/** 数量查询返回的别名 */
	String countName() default "";
}
```

> [!tip] Reference
>
> - [@Query 用法 - CSDN 博客](https://blog.csdn.net/u012894692/article/details/108331511)
> - [Spring Data JPA @Query | Baeldung](https://www.baeldung.com/spring-data-jpa-query)

### @Modifying

通知 _JPA_，这是一个 _update_ 或者 _delete_ 操作，在更新或者删除操作时，此注解必须加，否则会抛出异常

_QPQL_ 不支持 _insert_ 操作

Service 层必须加事务，不加事务也会报错

#### clearAutomatically = true 的作用

指可以清除底层持久化上下文，即 `entityManager` 这个类；JPA 底层实现有一级缓存，也就是在更新完数据库后，如果后面去用这个对象，再去查这个对象，这个对象是在一级缓存，但是并没有跟数据库同步，此时使用 `clearAutomatically=true`，就会刷新 Hibernate 的一级缓存， 否则在同一接口中，更新一个对象，接着查询这个对象，那么查出来的这个对象还是之前的没有更新前的状态

> [!note]
> 建议 `clearAutomatically` 和 `flushAutomatically` 都设置为 _true_

**If our modifying query changes entities contained in the persistence context, then this context becomes outdated.** One way to manage this situation is to [clear the persistence context](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html#clear--). By doing that, we make sure that the persistence context will fetch the entities from the database next time.

However, we don't have to explicitly call the *clear()* method on the *EntityManager*. We can just use the [*clearAutomatically* property](https://codingexplained.com/coding/java/spring-framework/updating-entities-with-update-query-spring-data-jpa) from the *@Modifying* annotation:

```java
@Modifying(clearAutomatically = true)
```

**This way, we make sure that the persistence context is cleared after our query execution.**

However, if our persistence context contained unflushed changes, clearing it would mean dropping the unsaved changes. Fortunately, there's another property of the annotation we can use in this case, *flushAutomatically*:

```java
@Modifying(flushAutomatically = true)
```

**Now the *EntityManager* is flushed before our query is executed.**

> [!tip] Reference
>
> - [Spring Data JPA @Modifying Annotation | Baeldung](https://www.baeldung.com/spring-data-jpa-modifying-annotation)

### @CreatedDate

### @CreatedBy

### @LastModifiedBy

### @LastModifiedDate

[Spring JPA 使用 @CreatedDate、@CreatedBy、@LastModifiedDate、@LastModifiedBy 自动生成时间和修改者 - 简书](https://www.jianshu.com/p/14cb69646195)
