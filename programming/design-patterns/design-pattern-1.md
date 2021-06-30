---
title: 从零开始设计模式第1章-UML图和设计模式七大原则
date: '2020-01-02T15:04:00.000Z'
categories: 从零开始设计模式
cover_img: images/common/2.jpg
feature_img: null
---

# 1 UML图和设计模式七大原则

前面简单介绍了设计模式中七大设计原则和23种设计模式。那么这一节，让我们用示例好好了解一下七大设计原则。你将会看到坏的示例和好的示例。

在此之前，需要简单学习一下UML。

## 1.UML

UML：Unified Modeling Language\(统一建模语言\)，是一种用于描述软件系统分析和设计的语言工具。能用图描述的地方就不要用文字去描述。

### 1.1 UML图分类

* 用例图
* 静态结构图：类图、对象图、包图、组件图、部署图
* 动态行为图：交互图\(时序图和协作图\)、状态图、活动图

### 1.2 类图

在学习设计模式中，我们核心使用到的是类图，类图是用于描述类与类之间的关系。下面是简单的介绍。

类的基本组成：类名、成员变量、成员方法

#### 1.2.1类的可访问性

![&#x7C7B;&#x7684;&#x53EF;&#x8BBF;&#x95EE;&#x6027;](../../.gitbook/assets/类的可访问性.png)

| **Character** | **Icon for field** | **Icon for method** | **Visibility** |
| :--- | :--- | :--- | :--- |
| `-` | ![img](https://s.plantuml.com/private-field.png) | ![img](https://s.plantuml.com/private-method.png) | private |
| `#` | ![img](https://s.plantuml.com/protected-field.png) | ![img](https://s.plantuml.com/protected-method.png) | protected |
| `~` | ![img](https://s.plantuml.com/package-private-field.png) | ![img](https://s.plantuml.com/package-private-method.png) | package private |
| `+` | ![img](https://s.plantuml.com/public-field.png) | ![img](https://s.plantuml.com/public-method.png) | public |

#### 1.2.2类之间关系：

* 依赖\(Dependency\)

   只要类中用到了另一个类，这两个类就是依赖关系\(直接朋友+陌生朋友\)

![Dependency](../../.gitbook/assets/依赖.png)

* 关联\(Association\)

   关联关系指的是类与类之间的联接，即我们平常说的一对一，一对多，多对多。 关联具有导航性，即双向关系或单向关系。

![Association](../../.gitbook/assets/关联1.png) ![Association](../../.gitbook/assets/关联2.png)

* 继承\(Generalization\) ![Generalization](../../.gitbook/assets/继承.png)
* 实现\(Realization\) ![Realization](../../.gitbook/assets/实现.png)
* 聚合\(Aggregation\)

   **整体和部分可以分开**，例如：电脑和键盘，鼠标。电脑没用了鼠标和键盘但是也可以用。这里整体指的是电脑，部分指的是鼠标、键盘。

![Aggregation](../../.gitbook/assets/聚合.png)

* 组合/复合\(Composite\)

   **整体和部分不可以分开**，例如：人和头。人不可以没有头。这里整体指的是人，部分指的是头。

![Composite](../../.gitbook/assets/组合.png)

### 1.3工具

插件工具[PlantUML](https://plantuml.com/zh/)

## 2.七大设计原则

### 2.1单一职责原则\(Single Responsibility Principle\)

一个类职责不能太多太大，应降低类的复杂度，一个类只负责一项职责。

就像我们的电子产品分类太大了，需要继续细分。 单一职责，并不只是对于类而言。对于类、接口、方法都应该尽量保证单一职责。为什么说尽量？因为职责是很难去划分清楚的，这里面最难的地方就在于如何划分清职责。 单一职责的核心思想是：不要存在多于一个原因导致类的变更。 &gt; There should never be more than one reason for a class to change. \*\*bad\*\* \`\`\`C\# public class Person { void single\(\); void jump\(\); void rap\(\); void playBasketball\(\); } \`\`\` \*\*good\*\* \`\`\`C\# class Student { void single\(\); void jump\(\); } class Teacher { void playBasketball\(\); } \`\`\` 说明：bad示例中，我们把单一职责看作一个人想学太多了\(教练：你什么都想学\)，什么都想学，只会什么都学不好。 \#\#\# 2.2接口隔离原则\(Interface Segregation Principle\) 一个接口功能不能太强太多。 这个跟单一职责有些像，只不过一个是类，一个是接口的方法不能太多。 对于接口来说，如果功能过强，当实现接口的时候，不必要的接口也必须实现，这其实太强制了。 接口隔离原则的核心思想是：客户端不应该依赖它不需要的接口，类间的依赖关系应该建立在最小的接口。 \*\*bad\*\* \`\`\`C\# public interface Person { void single\(\); void jump\(\); void rap\(\); void playBasketball\(\); } \`\`\` \*\*good\*\* \`\`\`C\# interface Student { void single\(\); void jump\(\); } interface Teacher { void playBasketball\(\); } \`\`\` 说明：bad示例中，划分不明细，一个接口太强了，并不是人人都喜欢唱跳rap、篮球🏀。 \#\#\# 2.3依赖倒置原则\(Dependence Inversion Principle\) 面向接口编程，利用多态的特性。 这是告诉我们，在变量类型声明、方法传递参数类型中应尽量使用接口或抽象类，而不是具体的类。 依赖传递的三种方式：构造方法、setter\(\)方法、声明依赖对象\(成员变量\) \*\*bad\*\* \`\`\`C\# class Student{ public void single\(\){ } } class Teacher{ public void single\(\){ } } class Demo{ void single1\(Student arg\){ arg.single\(\); } void single2\(Teacher arg\){ arg.single\(\); } } \`\`\` \*\*good\*\* \`\`\`C\# interface Person { void single\(\); } class Student implements Person{ public void single\(\){ } } class Teacher implements Person{ public void single\(\){ } } class Demo{ void single\(Person arg\){ arg.single\(\); } } \`\`\` 说明：bad示例中，在Demo类中每让一个人single\(\)，就要增加新single方法。方法传递的参数是具体的类，很不方便。在good中我们改进了一下，接口的形式作为形参，这样每次Demo类就不用改了。\*\*\(在变量类型声明、方法传递参数类型中应尽量使用接口或抽象类\)\*\* \#\#\# 2.4里氏替换原则\(Liskov Substitution Principle\) 如果子类不能完整的实现父类的方法，应断开父子继承的关系，通过聚合、组合、依赖来解决。 继承的缺点： 1. 侵入性 2. 降低代码灵活性 3. 增强耦合性

里氏替换原则的核心思想是：所有引用父类对象的地方，就能够使用其子类的对象。

**bad**

```text
class Father {

    long money(){
        return 10000000000L;
    }
}

class Son extends Father{

    @Override
    long money(){
        return 0L;
    }
}
```

**good**

```text
class Father {

    long money(){
        return 10000000000L;
    }
}

class Son extends Father{

    long sonMoney(){
        return 1L + money();
    }
}
```

说明：bad示例中，父亲本来有很多钱，孩子继承后父亲的东西后，没有要父亲的钱。good中我们进行了改进，孩子利用继承父亲的钱，赚到了更多的钱。

### 2.5开闭原则\(Open Close Principle\)

> 编程中遵守其它原则，以及使用设计模式的目的就是遵守**开闭原则**。

模块和函数应对扩展开放\(**提供者**\)，对修改关闭\(**使用者**\)。 `用抽象构建框架，用实现扩展细节。抽象约束` **bad**

```text
class Provider{

    void show(String grade) {
        if ("1".equals(grade)) {
            print("A");
        }else if ("2".equals(grade)) {
            print("B");
        } else if ("3".equals(grade)) {
            print("C");
        } else{
            print("D");
        }
    }

    void print(String s) {
        System.out.println(s);
    }
}

class User{
    public static void main(String args[]) {
        Provider demo = new Provider();
        demo.show("1");
        demo.show("2");
    }
}
```

**good**

```text
interface Provider{
    void print();
}

class A implements Provider{
    public void print(){
        System.out.println("A");
    }
}

class B implements Provider{
    public void print(){
        System.out.println("B");
    }
}

class C implements Provider{
    public void print(){
        System.out.println("C");
    }
}

class D implements Provider{
    public void print(){
        System.out.println("D");
    }
}

class User{
    public static void main(String args[]){
        User demo = new User();
        demo.show(new A());
        demo.show(new B());
    }

    void show(Provider grade){
        grade.print();
    }
}
```

说明：bad示例中，我们要打印不同内容，是通过if-else去判断，这样每增加内容就要写一次if-else。**如果有一天grade不用1234去判断了，改用abcd去判断了，那么我们就要改很多代码，同时User使用方也需要进行修改。** 开闭原则，主张的是提供方改动而不需要使用方改动。在good里，我们进行了改进，使用接口作为形参，这样需要调什么内容就传具体的类过来。新增内容，就新增一个实现类，使用方如果想要就用，不想用也不会有影响。

### 2.6迪米特法则\(Demeter Principle\)\(最少知道原则\)

> 一个类对自己依赖的类知道的越少越好。无论被依赖的类多么复杂，都应该将逻辑封装在方法的内部，通过public方法提供给外部。这样当被依赖的类变化时，才能最小的影响该类。

最少知道原则表达的是：只与直接朋友通信。 直接朋友：有耦合关系的类则称为朋友关系。耦合分为依赖、关联、聚合、组合等。例如一个类中**成员变量、方法参数、或者方法返回值中**用到了另一个类，则称这个两个类为直接朋友关系。 陌生朋友：不是直接朋友，也就是陌生朋友。例如一个类中的**局部变量、临时变量**用到了另一个类。

 迪米特法则希望我们能够尽量不要在类中的局部变量和临时变量用到另一个陌生的类，即只与直接朋友通信。这样可以降低类与类直接的\*\*耦合性\*\*。

**bad**

```text
class A{
    public void print(){
        System.out.println("A");
    }
}

class B{

    void print1(){
        // 局部变量中用到了类A
        A a = new A();
        a.print();
    }

}
```

**good**

```text
class A{
    public void print(){
        System.out.println("A");
    }
}

class B{

    // 直接朋友，成员变量用到了类A
    private A a;
    // 直接朋友，方法参数用到了类A
    void print1(A a){
        a.print();
    }
    // 直接朋友，返回值用到了类A
    A print2(){
        return new A();
    }
}
```

说明：bad示例中，在局部变量中用到了A类，这样耦合性太强了。

### 2.7合成复用原则\(Composite/Aggregate Reuse Principle\)

`尽量首先使用组合/聚合的方式，而不是使用继承。`

**bad**

```text
class A{
    public void print(){
        System.out.println("A");
    }

    public void work(){
        System.out.println("work");
    }
}

class B extends A{

}
```

**good**

```text
class A{
    public void print(){
        System.out.println("A");
    }

    public void work(){
        System.out.println("work");
    }
}

class B{

    // 成员变量用到了类A
    private A a;
    public B(A a){
        this.a = a;
    }

    // or
    private A a1 = new A();

    // 方法参数用到了类A
    void print1(A a){
        a.print();
    }
    // 返回值用到了类A
    A print2(){
        return new A();
    }
}
```

说明：bad示例中，类B直接继承了类A，但是类B并不需要类A中的work\(\)方法，这样耦合性太强了。good中改成了聚合、组合的方法，更灵活。

### 2.8总结

* 单一职责原则和接口隔离原则相似，讲究对类或接口进行细化。
* 里氏替换原则、迪米特法则、合成复用原则相似，讲究应尽量使用组合、聚合的方式，而不是继承的方式。
* 依赖倒置原则，讲究的面向接口和抽象编程。在变量类型声明、方法传递参数类型中应尽量使用接口或抽象类，而不是具体的类。
* 开闭原则，是七大原则的核心。讲究对扩展开放\(**提供者**\)，对修改关闭\(**使用者**\)。

