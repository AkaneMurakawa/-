---
title: C++编程——C++模板
date: '2018-10-25T10:55:13.000Z'
categories: C++编程
toc: true
comments: true
---

# C++编程——C++模板

模板就是泛型的一种编程思想 格式：

```cpp
template <typename T> 方法一
template <class T>方法二
template和typename是关键字
```

**作用域：仅对下边挨着的代码段有效** 例子：

```cpp
#include <iostream>
using namespace std;

template <typename T>
void fun(T a)
{
    cout << a << endl;
}

int main()
{
    fun(1);
    fun(12.3);
    system("pause");
    return 0;
}
```

## 函数模板的具体化

```cpp
struct Node
{
    int a;
};

template <typename T>
void fun(T a)
{
    cout << a << endl;
}
// 函数模板的具体化，对fun的特殊处理，单独的类型处理
template<> void fun<Node>(Node no)
{
    cout << no.a << endl;
}
```

## 类模板

```cpp
template<typename T>
class CPerson
{
public:
    T a;
    CPerson(T t)
    {
        a = t;
    }
};

int main()
{
    CPerson<char> c('a');
    system("pause");
    return 0;
}
```

