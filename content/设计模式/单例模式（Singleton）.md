---
created: 2022-05-10 10:06
tags: [设计模式]
---

### 特点

构造方法私有化，自身必须定义一个静态私有实例，提供一个静态的公有函数用于创建或获取该静态私有实例。

### 应用场景

- 在应用场景中，某类只要求生成一个对象的时候，如一个班中的班长、每个人的身份证号等。
- 当对象需要被共享的场合。由于单例模式只允许创建一个对象，共享该对象可以节省内存，并加快对象访问速度。如 Web 中的配置对象、数据库的连接池等。
- 当某类需要频繁实例化，而创建的对象又频繁被销毁的时候，如多线程的线程池、网络连接池等。

### 懒汉式与饿汉式（写法不推荐）

#### 懒汉式

该模式的特点是类加载时没有生成单例，只有当第一次调用 getlnstance 方法时才去创建这个单例。

```java
public class LazySingleton {
    private static volatile LazySingleton instance=null;    //保证 instance 在所有线程中同步
    private LazySingleton(){}    //private 避免类在外部被实例化

    public static synchronized LazySingleton getInstance() {
        //getInstance 方法前加同步
        if(instance==null) {
            instance=new LazySingleton();
        }
        return instance;
    }
}
```

如果编写的是多线程程序，则不要删除上例代码中的关键字 volatile 和 synchronized，否则将存在线程非安全的问题。虽然这两个关键字能保证线程安全，但是每次访问时都要同步，会影响性能，且消耗更多的资源，这是懒汉式单例的缺点。

#### 饿汉式

该模式的特点是类一旦加载就创建一个单例，保证在调用 getInstance 方法之前单例已经存在了。

```java
public class HungrySingleton {
    private static final HungrySingleton instance=new HungrySingleton();

    private HungrySingleton(){}

    public static HungrySingleton getInstance() {
        return instance;
    }
}
```

饿汉式单例在类创建的同时就已经创建好一个静态的对象供系统使用，以后不再改变，所以是线程安全的，可以直接用于多线程而不会出现问题。

### 推荐写法

#### 双重检查锁（懒汉式）

```java
public class DoubleCheckedLockingSingleton {
    /**
     * 实例化对象分3步：分配内存空间，初始化对象，将对象指向刚分配的内存空间
     * 有些编译器为了性能，可能会将上面的指令重排，别的线程就会得到一个未初始化的对象
     * volatile 可以防止实例化对象时指令重排
     */
    private volatile static DoubleCheckedLockingSingleton instance;

    /**
     * 私有化构造器，其他线程就不能直接 new 对象了
     */
    private DoubleCheckedLockingSingleton() {}

    /**
     * 用 synchronized 加锁，保证了线程安全
     * 只在第一次实例化的时候才加锁，提升性能
     * 执行双重检查是因为，有多个线程通过了第一次检查，当第一个线程创建了对象时，剩余的线程不再创建对象
     * @return
     */
    public static DoubleCheckedLockingSingleton getInstance() {
        if (null == instance) {
            synchronized (DoubleCheckedLockingSingleton.class) {
                if (null == instance) {
                    instance = new DoubleCheckedLockingSingleton();
                }
            }
        }
        return instance;
    }
}
```

#### 静态内部类

```java
public class Singleton {

    private Singleton() {}

    private static class SingletonInstance {
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return SingletonInstance.INSTANCE;
    }
}
```

采用了类装载的机制来保证初始化实例时只有一个线程。静态内部类在 Singleton 类被装载时并不会立即实例化，而是在调用 getInstance 方法，才会装载 SingletonInstance 类，从而完成 Singleton 的实例化。

类的静态属性只会在第一次加载类的时候初始化，所以在这里，JVM 帮助我们保证了线程的安全性，在类进行初始化时，别的线程是无法进入的。

优点：避免了线程不安全，延迟加载，效率高。

> [!link]- Reference
> [设计模式：单例模式 (关于饿汉式和懒汉式)](https://blog.csdn.net/yeyazhishang/article/details/90445330)
> 
> [单例模式的八种写法比较](http://cnblogs.com/zhaoyan001/p/6365064.html)
> 
> [Java 中的双重检查锁（double checked locking）](http://cnblogs.com/xz816111/p/8470048.html)
