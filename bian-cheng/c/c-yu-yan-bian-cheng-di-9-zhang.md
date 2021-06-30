---
title: C语言编程-命令行参数
date: '2018-09-15T03:55:13.000Z'
categories: C语言编程
toc: true
comments: true
---

# 第九章 标准的C语言主函数和命令行参数

在网上有很多种C语言主函数的写法，虽然有些运行起来并没有什么错误。但是在C99标准后，在标准的C语言主函数中，只有两种形式，我们应该尽量去遵守标准避免不必要的错误，形式如下：

```c
int main(void)
{

}

int main(int argc, char* argv[])
{
}
```

## 为什么要加void的？

有些教程是不加void的，但是在C语言里面对于参数列表**为空**的时候**，C语言表示这个函数可以传任意参数。而C++表示这个函数没有参数，**所以注意**C语言中没有参数的时候要加void。**

那今天就来说一下这个命令行参数的标准主函数，对于一个函数，我们可以从函数名和参数去了解该函数的作用。那首先我们来看看这两个参数表示什么。

## 参数意义

argc：是命令行总的参数个数。 argv\[\]：是argc个参数，也就是说argv数组的长度就是argc。**其中第0个参数是程序的全名** **argc: argument counter** **argv: argument vector**

当我们在Visual Studio中点击开始执行的时候，Visual Studio就会帮我们运行程序。这个过程，其实就是运行了可执行程序。Visual Studio会通过自己的方式，找到程序的路径，然后运行。比如我们在linux的Terminal中打开vim就直接输入vim了。在windows中我们要运行程序，也可以在终端里找到程序的路径然后回车执行。比如：

```text
C:\Users\xxxxx>C:\Software\MarkdownEditor.exe
```

**这里的命令行参数指的就是我们在Terminal输入的命令。**

其中，这里第0个参数默认是程序的全路径。我们可以打印出来看看。

```text
    printf("%d\n", argc);  // 结果：1

    for (int i = 0; i 


     argc; i++)
    {
        printf("%s\n", argv[i]);
    }
    //结果：E:\XXXXX\CLearn\x64\Debug\CLearn.exe
```

如果要传入更多的命令参数，我们也可以自己在Visual Studio设置，项目属性-调试-命令参数

```text
"command1" "command2" "command3"
```

