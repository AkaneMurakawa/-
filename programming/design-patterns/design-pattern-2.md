---
title: 从零开始设计模式第2章-创建型模式(五种)
date: '2020-01-06T00:35:00.000Z'
categories: 从零开始设计模式
cover_img: images/common/3.jpg
feature_img: null
---

# 2 创建型模式\(五种\)

## 1.创建型模式\(5种\)

* 单例模式\(Singleton Pattern\)
* 工厂模式\(Factory Pattern\)
* 抽象工厂模式\(Abstract Factory Pattern\)
* 原型模式\(Prototype Pattern\)
* 建造者模式\(Builder Pattern\)

### 1.1 单例模式\(Singleton Pattern\)

介绍：保证一个类只有一个对象实例。 使用场景： 1. 需频繁的创建和销毁类\(重量级对象\)，但又经常需要用到的。如：工具类、数据源、session工厂等。 2. 要求生产唯一序列号。 3. WEB 中的计数器，不用每次刷新都在数据库里加一次，用单例先缓存起来。 4. 创建的一个对象需要消耗的资源过多，比如 I/O 与数据库的连接等。

#### 1.1.1饿汉式\(静态常量\)

```text
public class SingletonDemo1 {

    public static void main(String args[]){
        Singleton1 instance1 = Singleton1.getInstance();
        Singleton1 instance2 = Singleton1.getInstance();
        System.out.println(instance1 == instance2);
    }
}

// 饿汉式(静态常量)
class Singleton1{

    // 1.私有化构造方法
    private Singleton1(){}

    // 2.静态变量
    private static final Singleton1 instance = new Singleton1();

    // 3.获取
    public static Singleton1 getInstance(){
        return instance;
    }

}
```

优点：写法简单，线程安全 缺点：没有懒加载\(lazy loading\)，可能造成内存浪费

#### 1.1.2饿汉式\(静态块\)

```text
public class SingletonDemo2 {

    public static void main(String args[]){
        Singleton2 instance1 = Singleton2.getInstance();
        Singleton2 instance2 = Singleton2.getInstance();
        System.out.println(instance1 == instance2);
    }
}

// 饿汉式(静态块)
class Singleton2{

    // 1.私有化构造方法
    private Singleton2(){}

    private static Singleton2 instance;

    // 2.静态块
    static {
        instance = new Singleton2();
    }

    // 3.获取
    public static Singleton2 getInstance(){
        return instance;
    }

}
```

优点：写法简单，线程安全 缺点：没有懒加载\(lazy loading\)，可能造成内存浪费

#### 1.1.3懒汉式\(同步方法\)

```text
public class SingletonDemo3 {

    public static void main(String args[]){
        Singleton3 instance1 = Singleton3.getInstance();
        Singleton3 instance3 = Singleton3.getInstance();
        System.out.println(instance1 == instance3);
    }
}

// 懒汉式(同步方法)
class Singleton3{

    // 1.私有化构造方法
    private Singleton3(){}

    private static Singleton3 instance;

    // 2.获取
    public static synchronized Singleton3 getInstance(){
        if (instance == null) {
                instance = new Singleton3();
        }
        return instance;
    }

}
```

优点：线程安全，延迟加载 缺点：效率较低

#### 1.1.4双重检查\(同步代码块\)

```text
public class SingletonDemo4 {

    public static void main(String args[]){
        Singleton4 instance1 = Singleton4.getInstance();
        Singleton4 instance4 = Singleton4.getInstance();
        System.out.println(instance1 == instance4);
    }
}

// 双重检查(同步代码块)
class Singleton4{

    // 1.私有化构造方法
    private Singleton4(){}

    // 2.volatile保证内存可见性，防止指令重排
    private static volatile Singleton4 instance;

    // 3.获取
    public static Singleton4 getInstance(){
        if (instance == null) {
            // 4.静态块lock的只能是class类
            synchronized (Singleton4.class){
                if (instance == null) {
                    // 5.赋值
                    instance = new Singleton4();
                }
            }
        }
        return instance;
    }

}
```

优点：线程安全，延迟加载，效率较高\(**推荐使用**\) 缺点：

#### 1.1.5静态内部类

```text
public class SingletonDemo5 {

    public static void main(String args[]){
        Singleton5 instance1 = Singleton5.getInstance();
        Singleton5 instance5 = Singleton5.getInstance();
        System.out.println(instance1 == instance5);
    }
}

// 静态内部类
class Singleton5{

    // 1.私有化构造方法
    private Singleton5(){}

    // 2.静态内部类(外部类被加载时候，静态内部类不会被加载)
    private static class SingletonInstance{
        private static final Singleton5 instance = new Singleton5();
    }

    // 3.获取
    public static Singleton5 getInstance(){
        return SingletonInstance.instance;
    }

}
```

优点：线程安全，延迟加载，效率较高\(**推荐使用**\) 缺点：

#### 1.1.6枚举

```text
public class SingletonDemo6 {

    public static void main(String args[]){
        Singleton6 instance1 = Singleton6.INSTANCE;
        Singleton6 instance6 = Singleton6.INSTANCE;
        System.out.println(instance1 == instance6);
    }
}

// 枚举
enum Singleton6{

    INSTANCE
}
```

优点：线程安全，防止反序列化重新创建新对象\(**推荐使用**\) 缺点：

#### 1.1.7单例模式应用

java.lang.Runtime

单例模式很好理解，接下来的设计模式则比较抽象，需要好好进行思考。

### 1.2 工厂模式\(Factory Pattern\)

介绍：定义一共用于创建对象的接口，让子类决定实例化哪一个类。工厂方法使一个类的实例化延迟到其子类。 使用场景： 1. 日志记录器：记录可能记录到本地硬盘、系统事件、远程服务器等，用户可以选择记录日志到什么地方。 2. 数据库访问，当用户不知道最后系统采用哪一类数据库，以及数据库可能有变化时。 3. 设计一个连接服务器的框架，需要三个协议，"POP3"、"IMAP"、"HTTP"，可以把这三个作为产品类，共同实现一个接口。

#### 1.2.1 分类

* 简单工厂模式，也就静态工厂模式
* 多个工厂模式

#### 1.2.2 示例

```text
/**
 * 产品
 */
public abstract class Product {

    public void method1(){}

    // 业务逻辑处理
    public abstract void method2();
}

public class ConcreateProduct1 extends Product {

    @Override
    public void method2() {
        // 业务逻辑处理
    }
}

public class ConcreateProduct2 extends Product {

    @Override
    public void method2() {
        // 业务逻辑处理
    }
}

/**
 * 创建者
 */
public abstract class Creator {

    public abstract <T extends Product> T createProduct(Class<T> clazz);
}

public class ConcreateCreator extends Creator {

    @Override
    public <T extends Product> T createProduct(Class<T> clazz) {
        Product product = null;
        try {
            product = (Product)clazz.forName(clazz.getName()).newInstance();
        }catch (ClassNotFoundException e){
        }catch (IllegalAccessException e){
        }catch (InstantiationException e){
        }
        return (T)product;
    }
}

public class Client {

    public static void main(String args[]){
        Creator creator = new ConcreateCreator();
        Product product1= creator.createProduct(ConcreateProduct1.class);
        Product product2= creator.createProduct(ConcreateProduct2.class);
    }
}
```

![factory](../../.gitbook/assets/factory.png) 解读：Creator通过传入不同的产品类型来创建不同的产品。

工厂模式在项目中使用非常频繁，需好好思考如何运用。

### 1.3 抽象工厂模式\(Abstract Factory Pattern\)

介绍：提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类 使用场景： 1. QQ 换皮肤，一整套一起换。 2. 生成不同操作系统的程序。

#### 1.3.1 示例

```text
/**
 * 产品A
 */
public abstract class AbstractProductA {

    public void method1(){}

    // 业务逻辑处理
    public abstract void method2();
}

/**
 * 产品B
 */
public abstract class AbstractProductB {

    public void method1(){}

    // 业务逻辑处理
    public abstract void method2();
}

public class ProductA1 extends AbstractProductA {

    @Override
    public void method2() {
        // 业务逻辑处理
    }
}

public class ProductA2 extends AbstractProductA {

    @Override
    public void method2() {
        // 业务逻辑处理
    }
}

public class ProductB1 extends AbstractProductB {

    @Override
    public void method2() {
        // 业务逻辑处理
    }
}

public class ProductB2 extends AbstractProductB {

    @Override
    public void method2() {
        // 业务逻辑处理
    }
}

/**
 * 创建者
 */
public abstract class AbstractFactryCreator {

    public abstract AbstractProductA createProductA();

    public abstract AbstractProductB createProductB();
}

public class FactryCreator1 extends AbstractFactryCreator {

    @Override
    public AbstractProductA createProductA() {
        return new ProductA1();
    }

    @Override
    public AbstractProductB createProductB() {
        return new ProductB1();
    }
}

public class FactryCreator2 extends AbstractFactryCreator {

    @Override
    public AbstractProductA createProductA() {
        return new ProductA2();
    }

    @Override
    public AbstractProductB createProductB() {
        return new ProductB2();
    }
}

public class Client {
    public static void main(String args[]){
        AbstractFactryCreator creator1 = new FactryCreator1();
        AbstractProductA productA1 = creator1.createProductA();
        AbstractProductB productB1 = creator1.createProductB();

        AbstractFactryCreator creator2 = new FactryCreator2();
        AbstractProductA productA2 = creator2.createProductA();
        AbstractProductB productB2 = creator2.createProductB();
    }
}
```

![factory](../../.gitbook/assets/abstractfactory.png) 解读：与工厂模式不同的是，抽象工厂模式把工厂抽象起来，通过不同的AbstractFactryCreator工厂创建不同的产品。

抽象工厂模式和工厂模式有点像，需要好好理解和区分。下面来看看原型模式

### 1.4 原型模式\(Prototype Pattern\)

介绍：用于创建重复的对象，同时又能保证性能。 使用场景： 1. 资源优化场景。类初始化需要消化非常多的资源，这个资源包括数据、硬件资源等。 2. 性能和安全要求的场景。通过 new 产生一个对象需要非常繁琐的数据准备或访问权限，则可以使用原型模式。 3. 一个对象多个修改者的场景。一个对象需要提供给其他对象访问，而且各个调用者可能都需要修改其值时，可以考虑使用原型模式拷贝多个对象供调用者使用。 4. 在实际项目中，原型模式很少单独出现，一般是和工厂方法模式一起出现，通过 clone 的方法创建一个对象，然后由工厂方法提供给调用者。

#### 1.4.1 示例

```text
/**
 * Mail
 * Cloneable用于标记可拷贝，无实现方法
 */
public class Mail implements Cloneable{
    /**
     * 默认是浅拷贝
     * @return
     */
    @Override
    protected Object clone(){
        Mail mail = null;
        try{
            mail = (Mail)super.clone();
        }catch (CloneNotSupportedException e){

        }
        return mail;
    }
}

public class Client {
    public static void main(String args[]){
       Mail template = new Mail();
       Mail template0 =  (Mail)template.clone();
       Mail template1 =  (Mail)template.clone();
       Mail template2 =  (Mail)template.clone();
       Mail template3 =  (Mail)template.clone();
       Mail template4 =  (Mail)template.clone();
       Mail template5 =  (Mail)template.clone();
    }
}
```

解读：通过实现Cloneable接口，并重写Object基类的clone\(\)方法，实现了类的复制。 原型模式从堆内存二进制流进行拷贝，避免了构造函数的执行，大大提高了效率。

#### 1.4.2 实现深拷贝的方式

1. 重写clone\(\)方法
2. 通过序列化和反序列化方式

使用原型模式一定要注意浅拷贝和深拷贝问题

### 1.5 建造者模式\(Builder Pattern\)

介绍：将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示\(顺序\)。 使用场景： 1. 方法相同，但是**组合顺序不同**

#### 1.5.1 示例

```text
/**
 * 产品
 */
public abstract class Computer {

    protected String board;
    protected String display;
    protected String mOs;

    protected Computer(){
    }


    public void setBoard(String board){
        this.board=board;
    }

    public void setDisplay(String display) {
        this.display = display;
    }

    public abstract void setOs() ;
}

public class MacBook extends Computer{
    @Override
    public void setOs() {
        System.out.println("Mac OS X");
    }
}

public abstract class Builder {

    abstract void buildBoard(String board);

    abstract void buildDisplay(String display);

    abstract void buildOs();

    abstract Computer build();
}

public class MacBookBuilder extends Builder {

    private Computer macBook = new MacBook();

    @Override
    void buildBoard(String board) {
        macBook.setBoard(board);
    }

    @Override
    void buildDisplay(String display) {
        macBook.setDisplay(display);
    }

    @Override
    void buildOs() {
        macBook.setOs();
    }

    @Override
    Computer build() {
        return macBook;
    }
}

/**
 * 指挥者
 */
public class Director {

    private Builder builder = null;

    public Director(Builder builder) {
        this.builder = builder;
    }

    public void construct(String board,String display){
        builder.buildDisplay(display);
        builder.buildBoard(board);
        builder.buildOs();
    }
}

public class Client {

    public static void main(String args[]) {
        Builder builder = new MacBookBuilder() ;

        Director director = new Director(builder);
        director.construct("英特尔主板", "Retina显示器");

        Computer macBook = builder.build();
    }
}
```

![factory](../../.gitbook/assets/builder.png) 解读：产品Computer并不是直接通过builder就创建完毕，可以看到builder创建的产品是不完整的。在最后由Director指挥完成最后的一个设置才是真正的产品。

#### 1.5.2 建造者模式使用

StringBuilder

