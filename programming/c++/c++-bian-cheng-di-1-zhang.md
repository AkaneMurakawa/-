---
title: C++编程——C++中的类
date: '2018-10-12T05:55:13.000Z'
categories: C++编程
toc: true
comments: true
---

# C++编程——C++中的类

C++中的类和Java中的类在形式上还有一些细节上是大不相同。当然我也不会对比所有的东西，因为我现在学习的是C++，所以主要说的也是C++。

## C++中类的声明和创建

```cpp
class CPeople
{
public:
    char cName[12];
    void setName(char* name)
    {
        strcpy(cName, name);
    }
    void show()
    {
        cout << "name:" << cName << endl;
    }
};

int main()
{
    CPeople p;
    char c[12] = "fdsaf\0";
    p.setName(c);
    p.show();
    CPeople* p1 = new CPeople; // 通过new的形式

    system("pause");
    return 0;
}
```

1.从形式上来看，**C++中声明了一个class的类型，注意结束的时候是有分号；的** 2.权限上也大有不同，C++只有三种权限，默认是private，而Java有四种，默认就是默认的权限。 3.权限的使用上，C++使用的以下的形式，个人觉得这样使代码显得更清晰。

```text
public:
protected:
private:
```

## C++中的权限

在C++中只有三种权限，类默认是private，结构体默认是public。

|  | 类内可见 | 子类可见 | 类外可见 |
| :--- | :--- | :--- | :--- |
| private | √ |  |  |
| protected | √ | √ |  |
| public | √ | √ | √ |

下面是Java中的权限，看看就好。

|  | 同一个类 | 同一个包 | 不同包的子类 | 不同包的非子类 |
| :--- | :--- | :--- | :--- | :--- |
| private | √ |  |  |  |
| Default | √ | √ |  |  |
| protected | √ | √ | √ |  |
| public | √ | √ | √ | √ |

## 友元

在类中如果我们设置了属性是private，但是有特殊的情况是，一些类不可以用但是某一个类可以用。这个时候将这个类或者函数声明为友元，那么这个类或者函数就可以访问了。 **关键字：friend**

```cpp
#include <iostream>
using namespace std;

class CSTU
{
public:
    CSTU()
    {}
    ~CSTU()
    {}

private:
    int age;
    void fun()
    {
        age = 12;
        cout << age << endl;
    }
    friend void fun1(); // 友元函数 类内成员对于这个函数是可见的。
    friend class MyClass; // 友元类
};

//  友元函数
void fun1()
{
    CSTU stu;
    stu.fun();
}
// 友元类
class MyClass
{
public:
    MyClass()
    {}
    ~MyClass()
    {}
private:
    void fun1()
    {
        CSTU stu;
        stu.fun();
    }
};


int main()
{
    fun1();
    system("PAUSE");
    return 0;
}
```

## 构造函数和析构函数

构造函数通常是用来初始化一些参数，在对象创建的时候调用。 1.如果是栈区对象，则会创建。 2.如果是堆区对象，声明指针类型只有用**new的时候才会创建对象**。

```cpp
CStu stu(12);
CStu* stu = new CStu(12);
```

在Java中也有构造函数，不过C++却多了一个析构函数用于释放。C++中的释放值得注意的是，以前用C我们释放一段空间是用**free\(\)**，而C++中用的**delete，并且释放要对应类型。**，比如：

```cpp
delete p;
delete[] pArr; //申请一段数组空间。
```

而析构函数则在函数名前加**~**，**只能有一个析构函数，不能重载,这个构造函数区别一下。构造函数是可以有多个重载的**

```cpp
MyClass::MyClass()
{
}

MyClass::~MyClass()
{
}
```

### 成员变量初始化问题

**在C++中，成员变量是没有默认的初始值的，并且不能初始化\(只有静态变量数据成员才可以在类中初始化\)**，而在Java中是成员变量如果不给是由默认值的，并且可以直接访问。造成这种差别的原因我觉得可能是由于C++是要自己申请空间，而Java则是交给了JVM。

## C++中的访问符

在Java中访问成员都是用**点操作符**,而在C++中则有多种方式。 1.如果是引用类型，用点操作符。

```text
stu.age
```

2.如果是指针类型，用-&gt;

```text
stu->age
```

3.C++中还有一个**:: 作用域限定符**，申明变量或函数所属类的符号。之所以会有这个符号，是因为C++中的重载。

```text
CStu::CStu(int a)
{

}
```

## malloc和new， free和delete

1.new会触发调用构造函数，而malloc不会。 2.delete会触发调用析构函数，而free不会。

## this指针

当类中函数的形参和我们的成员变量名相同的时候，为了区别同名，C++和Java中都设计了this表示当前对象指针。

```text
this.age // Java
this->age // C++
```

