---
title: C++编程——C++中的多态
date: '2018-10-24T03:55:13.000Z'
categories: C++编程
toc: true
comments: true
---

# C++编程-第7章

多态：父类的指针，调用子类的函数。即：父类的一个函数指针可以有多种状态。可以定义一个父类的类型指针，通过自类的构造函数来为其创建对象。 关键字： virtual 1.加了virtual之后就叫做**虚函数了。**通过虚函数，对象类型就会在程序运行时动态绑定。 2.子类重写的函数，默认是虚函数，可以不加virtual。 3.重写发生在父子类。 **4.协变：返回值类型不同\(只能是当前所在类\) —— 协变** 5.内联函数和构造函数不能是虚函数。

```cpp
#include <iostream>
using namespace std;

class Father
{
public:
    int a;
    Father()
    {
    }
    virtual void show()
    {
        cout << "I am Father" << endl;
    }
};
class Son : public Father
{
public:
    int a;
    Son()
    {
    }
    void show()
    {
        cout << "I am Son" << endl;
    }
};

int main()
{
    cout << "多态" << endl;
    Father *ming = new Son();
    ming->show();
    system("pause");
    return 0;
}
```

## 虚表

就是有一个表，用于装虚函数的地址。因为我们的函数就是通过地址去调用的。如果子类有重写，创建对象的时候，就会覆盖掉这个函数地址。 调用过程: 看父类调用的函数是不是虚函数，如果是则到虚表里去找并指向。如果不是则执行自己的。

## 取虚表的地址内容

 \`\`\`c++ int main\(\) { cout&lt;&lt; "多态" &lt;&lt; endl; Father \*ming=new Son\(\); ming-&gt;show\(\); typedef void \(\*fun\)\(\); \(\(fun\)\(\*\(\(int\*\)\*\(int\*\)ming + 0\)\)\)\(\); system\("pause"\); return 0; } \`\`\`

## 抽象类和接口

在开发中，我们往往会将重复使用的一些东西定义在一起，这就是我们的接口。我们如果开发一个类似的东西的时候，就只要继承这个类就行了。不过Java是单继承和多实现的，而C++则是可以多继承，但是这个思想是一样的。

无论是抽象类还是接口都是不能被实例化的，在Java中用的是关键字abstract表示抽象，如果是抽象方法则不能被实现，只有在子类继承的时候才能被实现。在C++的抽象这个概念则用virtual来表示。 下面是声明一个纯虚函数

```text
virtual void fun（） = 0;
```

Java中就比较简单了，直接用关键字abstract和interface，不过Java中没有函数声明这个概念，为了区别函数声明我猜所以C++才会用=0这种设计吧。

## 虚继承

**问题：**当我们继承了一个B和C，而B，C又分别继承了A之后，在访问时候就会出现访问不明确的问题。 解决方法：用虚继承。在继承的时候加上关键字virtual.类似静态成员的思想，所有继承的子类共用一个父类。普通的继承是复制父类的东西过来一份。

```cpp
class B : virtual public A
{}
```

实例：

```cpp
#include <iostream>
using namespace std;

class A
{
public:
    int a;
};
class B : virtual public A
{
public:

};
class C : virtual public A
{
public:

};
class D : public B , C
{
public:

};


int main()
{
    D d;
    d.a;
    system("pause");
    return 0;
}
```

