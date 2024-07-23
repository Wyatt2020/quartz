---
created: 2024-07-21 22:57
modified: 2024-07-21 22:58
tags: [设计模式]
---

定义了算法族，分别封装起来，让它们之间可以互相替换

抽象策略类

```java
//飞行
public interface FlyBehavior {
    void fly();
}

//叫声
public interface QuackBehavior {
    void quark();
}
```

具体策略

```java
public class FlyNoWay implements FlyBehavior {
    @Override
    public void fly() {
        System.out.println("鸭鸭不会飞");
    }
}

public class FlyWithWings implements FlyBehavior {
    @Override
    public void fly() {
        System.out.println("芜湖起飞！");
    }
}

public class Quack implements QuackBehavior {
    @Override
    public void quark() {
        System.out.println("嘎嘎嘎");
    }
}

public class Zizi implements QuackBehavior {
    @Override
    public void quark() {
        System.out.println("吱吱吱");
    }
}
```

鸭子 - 超类

```java
public abstract class Duck {
    //飞行
    FlyBehavior flyBehavior;
    //叫声
    QuackBehavior quackBehavior;

    /**
     * 每一种鸭子外观不同，所以是抽象方法，由子类去实现如何展示外观
     */
    public abstract void display();

    /**
     * 所有鸭子都会游泳，所以由超类实现这部分代码
     */
    public void swim() {
        System.out.println("开始游泳");
    }

    /**
     * 不是所有鸭子都会飞，委托给飞行类
     */
    public void preformFly() {
        flyBehavior.fly();
    }

    /**
     * 不是所有鸭子都会叫，委托给叫声类
     */
    public void preformQuack() {
        quackBehavior.quark();
    }

    /**
     * set 方法方便子类动态设定行为
     * @param flyBehavior
     */
    public void setFlyBehavior(FlyBehavior flyBehavior) {
        this.flyBehavior = flyBehavior;
    }

    public void setQuackBehavior(QuackBehavior quackBehavior) {
        this.quackBehavior = quackBehavior;
    }
}
```

玩具鸭

```java
public class ToyDuck extends Duck {
    /**
     * 重写超类的无参构造器，设置具体行为
     */
    public ToyDuck() {
        //玩具鸭不会飞
        flyBehavior = new FlyNoWay();
        //玩具鸭吱吱叫
        quackBehavior = new Zizi();
    }

    @Override
    public void display() {
        System.out.println("是一只黄色的玩具橡皮鸭");
    }
}
```

测试类

```java
public class DuckDemo {
    public static void main(String[] args) {
        Duck toyDuck = new ToyDuck();
        toyDuck.display();
        toyDuck.swim();
        toyDuck.preformFly();
        toyDuck.preformQuack();
        //改变鸭子的飞行行为
        toyDuck.setFlyBehavior(new FlyWithWings());
        toyDuck.preformFly();
        //改变鸭子的叫声行为
        toyDuck.setQuackBehavior(new Quack());
        toyDuck.preformQuack();
    }
}
```
