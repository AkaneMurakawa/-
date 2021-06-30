# 4 字节码插桩

## 用途

实现代码零侵入

## 字节码插桩所用到的技术

1. javaagent 代理拦截\(插桩的入口\)
2. javassist 字节码修改工具\(怎么插\)

### Java Agent

![image.png](../../.gitbook/assets/java14.png)

### ProxyGenerator

```text
package sun.misc;

public class ProxyGenerator {
    private static final int CLASSFILE_MAJOR_VERSION = 49;
    private static final int CLASSFILE_MINOR_VERSION = 0;
    private static final int CONSTANT_UTF8 = 1;
    private static final int CONSTANT_UNICODE = 2;
    private static final int CONSTANT_INTEGER = 3;
    private static final int CONSTANT_FLOAT = 4;
    private static final int CONSTANT_LONG = 5;
    private static final int CONSTANT_DOUBLE = 6;
    private static final int CONSTANT_CLASS = 7;
    private static final int CONSTANT_STRING = 8;
    private static final int CONSTANT_FIELD = 9;
    private static final int CONSTANT_METHOD = 10;
    private static final int CONSTANT_INTERFACEMETHOD = 11;
    private static final int CONSTANT_NAMEANDTYPE = 12;
    private static final int ACC_PUBLIC = 1;
    private static final int ACC_PRIVATE = 2;
    private static final int ACC_STATIC = 8;
    private static final int ACC_FINAL = 16;
    private static final int ACC_SUPER = 32;
    private static final int opc_aconst_null = 1;
    private static final int opc_iconst_0 = 3;
    private static final int opc_bipush = 16;
    private static final int opc_sipush = 17;
    private static final int opc_ldc = 18;
    private static final int opc_ldc_w = 19;
    private static final int opc_iload = 21;
    private static final int opc_lload = 22;
    private static final int opc_fload = 23;
    private static final int opc_dload = 24;
```

### 查看字节码

or javap

![image.png](../../.gitbook/assets/java15.png)

![image.png](../../.gitbook/assets/java16.png)

### Javassit

Javassit是一个开源的分析、编辑和创建Java字节码的类库。其主要的优点在于简单，而且快速。直接使用Java编码的形式、而不需要了解虚拟机指令，就能动态改变类的结构，或者动态生成。

注：也可以使用ASM实现，但需要会操作字节码指令，学习成本高

|  | 运行时.jar | javaagent.jar |
| :--- | :--- | :--- |
| 入口方法 | main\(String\[\]\) | premain\(String, instrumcent\) |
| 参数 | main-class | premain-class |
| 运行 | java -jar xx.jar | java -jar xxx.jar -javaagent:xx.jar |

现在当我们看到-javaagent:xx.jar时起码知道这个东西是什么了

## 阅读

[鲁班大叔](https://www.bilibili.com/video/BV1NC4y1a7P2?from=search&seid=4318814200044514244)

[分布式追踪系统SkyWalking](https://www.jianshu.com/p/2fd56627a3cf)

[SkyWalking官网](https://skywalking.apache.org/zh/)

[深入JVM，字节码](https://www.bilibili.com/video/BV1yx411R7RZ?from=search&seid=4318814200044514244)

