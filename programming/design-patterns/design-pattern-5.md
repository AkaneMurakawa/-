---
title: 从零开始设计模式第5章-其他模式(1种)
date: '2020-03-21T22:37:00.000Z'
categories: 从零开始设计模式
cover_img: images/common/1.jpg
feature_img: null
---

# 5 其他模式\(1种\)

## 1.其他模式\(1种\)

* 空对象模式\(Null Object Pattern\)

### 1.1 空对象模式\(Null Object Pattern\)

介绍：一个空对象取代 NULL 对象实例的检查。提供默认值，默认不做任何事情。 使用场景： 1. 有多个子类共有的方法，且逻辑相同。 2. 重要的、复杂的方法，可以考虑作为模板方法。

#### 1.1.1示例

```text
/**
 * 命令
 */
public interface Command {

    /**
     * 执行
     */
    void execute();

    /**
     * 撤销
     */
    void undo();
}

public class LightOffCommand implements Command {

    @Override
    public void execute() {
        System.out.println("light off");
    }

    @Override
    public void undo() {
        System.out.println("light on");
    }
}

public class LightOnCommand implements Command {

    @Override
    public void execute() {
        System.out.println("light on");
    }

    @Override
    public void undo() {
        System.out.println("light off");
    }
}

/**
 * 没有任何命令，空执行，用于初始化。
 * 可以省略对空的判断
 */
public class NullObjectCommand implements Command {

    @Override
    public void execute() {
    }

    @Override
    public void undo() {
    }
}

public class Client {

    public static void main(String[] args) {

        Command[] commands = new Command[2];
        for (int i = 0; i < commands.length; i++) {
            // 默认值，减少了空的判断
            commands[i] = new NullObjectCommand();
        }

    }
}
```

![nullobject](../../.gitbook/assets/nullobject.png) 解读：NullObjectCommand没有任何命令，空执行，用于初始化，可以省略对空的判断。

