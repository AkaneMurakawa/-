---
title: C++编程——C++中的运算符重载
date: '2018-10-18T03:55:13.000Z'
categories: C++编程
toc: true
comments: true
---

# C++编程-第5章

## 运算符重载

在C++中如果是普通的数据类型，用运算符进行计算的时候是可以，那我们的类呢？如果我们要进行运算要怎么样呢？这里就需要我们进行运算符重载，运算符其实是一种函数，然后函数重载。

关键字： operator，必须至少有一个**类类型的形参。** 格式：返回值 operator+运算符\(参数列表\) 返回值的作用：连续运算 注意：必须是支持的运算符，例如几个不能重载的：

```text
::
.*
?:
```

例子

```cpp
#include <iostream>
#include <stdio.h>
using namespace std;

class CStu
{
public:
    int* a;
    CStu() 
    {
        a = new int[2];
        a[0] = 12;
        a[1] = 43;
    } 
    ~CStu()
    {
        delete[] a;
    }
    CStu(const CStu&amp; stu)
    {
        this->a = new int[2];
        memcpy(this->a, stu.a, 8);
    }
};

CStu fun() {
    CStu stu;
    return stu;
}

inline void inlineTest()
{
    cout << "this is Inline Function" << endl;
}

int operator+(CStu&amp; st1, int a)
{
    st1.a[0] += a;
}

int operator+(int a, CStu&amp; st1)
{
    st1.a[0] += a;
}

int main()
{
    CStu st1;
    int a = 12;
    st1 + a; // 运算符重载

    system("pause");
    return 0;
}
```

## 类内重载和类外重载

类内重载，默认左边是类。

```cpp
class CStu
{
public:
    int nAge;
    CStu()
    {
        nAge = 43;
    }
    int operator+(int a) // 类 + a,默认左边是类
    {
        return (this->nAge + a); 
    }
};

int main()
{
    CStu st;
    st + 12;
    return 0;
}
```

ostream和istream运算符重载

```cpp
#include <iostream>
using namespace std;

class Per
{
private:
    int age;
public:
    Per()
    {
        age = 10;
    }
    friend ostream&amp; operator << (ostream&amp; os, Per&amp; per);
    friend istream&amp; operator >> (istream&amp; is, Per&amp; per);
};

ostream&amp; operator << (ostream&amp; os, Per&amp; per)
{
    os << per.age;
    return os;
}

istream&amp; operator >> (istream&amp; is, Per&amp; per)
{
    is >> per.age;
    if (is.fail())
    {
        cout << "fail" << endl;
        per.age = 0;
    }
    return is;
}

int main()
{
    Per per;
    cout << per << endl;
    cout << "This is a test for operator" << endl;
    cin >> per;
    cout << per << endl;
    system("pause");
    return 0;
}
```

