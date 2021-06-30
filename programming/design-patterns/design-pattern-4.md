---
title: 从零开始设计模式第4章-行为型模式(十一种)
date: '2020-03-15T22:37:00.000Z'
categories: 从零开始设计模式
cover_img: images/common/5.jpg
feature_img: null
---

# 4 行为型模式\(十一种\)

## 1.行为型模式\(11种\)

* 模板模式\(Template Pattern\)
* 命令模式\(Command Pattern\)
* 访问者模式\(Visitor Pattern\)
* 迭代器模式\(Iterator Pattern\) 
* 观察者模式\(Observer Pattern\)
* 中介者模式\(Mediator Pattern\)
* 备忘录模式\(Memento Pattern\)
* 解释器模式\(Interpreter Pattern\)
* 状态模式\(State Pattern\)
* 策略模式\(Strategy Pattern\)
* 职责链模式\(Chain of Responsibility Pattern\)

### 1.1 模板模式\(Template Method Pattern\)

介绍：也叫模板方法模式。定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。模板方法使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。一般模板方法都加上final关键字，防止子类重写模板方法。 使用场景： 1. 有多个子类共有的方法，且逻辑相同。 2. 重要的、复杂的方法，可以考虑作为模板方法。

**模板模式的钩子方法** 在抽象类中，可以定义一个方法，它默认不做任何事情，子类可以视情况覆盖它，该方法称为"钩子"。

#### 1.1.1示例

```text
public abstract class AbstractHandler<T> {

    /**
     * 模板方法
     */
    public final String process(T type, String message){
        if (match(type)){
            process(message);
            return "Y";
        }else if (match()){
            process(message);
            return "Y";
        }else {
            System.out.println("unknow type");

        }
        return "N";
    }

    protected abstract String process(String message);

    protected abstract boolean match(T type);

    /**
     * 钩子方法
     * @return
     */
    public boolean match(){
        return false;
    }
}

public enum MessageTypeEnum {

    ORDER("order", "订单"),

    WAREHOUSE("warehouse", "仓库");

    private String type;

    private String desc;

    MessageTypeEnum(){}

    MessageTypeEnum(String type, String desc) {
        this.type = type;
        this.desc = desc;
    }
}

public class OrderHandler extends AbstractHandler<MessageTypeEnum> {

    @Override
    protected String process(String message) {
        System.out.println(message);
        return "Y";
    }

    @Override
    protected boolean match(MessageTypeEnum type) {
        return MessageTypeEnum.ORDER == type;
    }
}

public class WareHouseHandler extends AbstractHandler<MessageTypeEnum> {

    @Override
    protected String process(String message) {
        System.out.println(message);
        return "Y";
    }

    @Override
    protected boolean match(MessageTypeEnum type) {
        return MessageTypeEnum.WAREHOUSE == type;
    }
}

public class Client {

    public static void main(String[] args) {
        AbstractHandler<MessageTypeEnum> order = new OrderHandler();
        order.process(MessageTypeEnum.ORDER, "order");

        AbstractHandler<MessageTypeEnum> warehousse = new WareHouseHandler();
        warehousse.process(MessageTypeEnum.WAREHOUSE, "warehousse");
    }
}
```

![template](../../.gitbook/assets/template.png) 解读：

#### 1.1.2 模板模式使用

Spring IOC容器初始化

### 1.2 命令模式\(Command Pattern\)

介绍：将一个请求封装成一个对象，从而使您可以用不同的请求对客户进行参数化。 使用场景： 1. GUI 中每一个按钮都是一条命令。 2. 模拟 CMD。 3. 系统需要支持命令的撤销\(Undo\)操作和恢复\(Redo\)操作。

#### 1.2.1示例

```text
/**
 * 接收者
 */
public class LightReceiver {

    public void on(){
        System.out.println("light on");
    }

    public void off(){
        System.out.println("light off");
    }
}

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

public class LightOffCommand implements Command{

    private LightReceiver receiver;

    public LightOffCommand(LightReceiver receiver){
        this.receiver = receiver;
    }

    @Override
    public void execute() {
        receiver.off();
    }

    @Override
    public void undo() {
        receiver.on();
    }
}

public class LightOnCommand implements Command{

    private LightReceiver receiver;

    public LightOnCommand(LightReceiver receiver){
        this.receiver = receiver;
    }

    @Override
    public void execute() {
        receiver.on();
    }

    @Override
    public void undo() {
        receiver.off();
    }
}

/**
 * 没有任何命令，空执行，用于初始化。
 * 可以省略对空的判断
 */
public class NoCommand implements Command{

    @Override
    public void execute() {
    }

    @Override
    public void undo() {
    }
}

/**
 * 调用者
 */
public class Invoker {

    private Command[] onCommands;
    private Command[] offCommands;

    // 撤销命令
    private Command undoCommand;

    private static final int SIZE = 5;

    public Invoker() {
        onCommands = new Command[SIZE];
        offCommands = new Command[SIZE];
        for (int i = 0; i < SIZE; i++) {
            onCommands[i] = new NoCommand();
            offCommands[i] = new NoCommand();
        }
    }

    public void setCommand(int index, Command onCommand, Command offCommand){
        onCommands[index] = onCommand;
        offCommands[index] = offCommand;
    }

    public void on(int index){
        onCommands[index].execute();
        // 记录上次命令
        undoCommand = onCommands[index];
    }

    public void off(int index){
        offCommands[index].execute();
        // 记录上次命令
        undoCommand = offCommands[index];
    }

    // revert
    public void undo(){
        undoCommand.undo();
    }
}

public class Client {

    public static void main(String[] args) {

        LightReceiver receiver = new LightReceiver();
        Command lightOnCommand = new LightOnCommand(receiver);
        Command lightOffCommand = new LightOffCommand(receiver);

        Invoker invoker = new Invoker();
        invoker.setCommand(0, lightOnCommand, lightOffCommand);
        invoker.on(0);
        invoker.off(0);
        invoker.undo();
    }
}
```

![command](../../.gitbook/assets/command.png) 解读：通过调用者调用接受者执行命令，顺序：调用者→接受者→命令

#### 1.2.2 命令模式使用

Jdbc Template

### 1.3 访问者模式\(Visitor Pattern\)

介绍：将数据结构与数据操作分离。 使用场景： 1. 对象结构中对象对应的类很少改变，但经常需要在此对象结构上定义新的操作。 2. 需要对一个对象结构中的对象进行很多不同的并且不相关的操作，而需要避免让这些操作"污染"这些对象的类，也不希望在增加新操作时修改这些类。

#### 1.3.1示例

```text
/**
 * 访问者
 */
public abstract class Action {

    public abstract void getResultMan(Person person);

    public abstract void getResultWomen(Person person);
}

public class Success extends Action{
    @Override
    public void getResultMan(Person person) {
        System.out.println(person.getName() + "success");
    }

    @Override
    public void getResultWomen(Person person) {
        System.out.println(person.getName() + "success");
    }
}

public class Fail extends Action{
    @Override
    public void getResultMan(Person person) {
        System.out.println(person.getName() + "fail");
    }

    @Override
    public void getResultWomen(Person person) {
        System.out.println(person.getName() + "fail");
    }
}

public abstract class Person {

    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Person(String name) {
        this.name = name;
    }

    public abstract void accept(Action action);
}

public class Man extends Person {

    public Man(String name) {
        super(name);
    }

    /**
     * 双分派
     * Man在Action中作为参数传递(第一次分派)
     * Man在方法中调用Action，同时将自己(this)作为参数传入(第二次分派)
     */
    @Override
    public void accept(Action action) {
        action.getResultMan(this);
    }
}

public class Women extends Person {

    public Women(String name) {
        super(name);
    }

    @Override
    public void accept(Action action) {
        action.getResultWomen(this);
    }
}

public class ObjectStructure {

    private List<Person> people = new LinkedList<>();

    public void attach(Person person){
        people.add(person);
    }

    public void detach(Person person){
        people.remove(person);
    }

    public void disPlay(Action action){
        people.forEach( person -> {
            person.accept(action);
        });
    }
}

public class Client {

    public static void main(String[] args) {
        ObjectStructure client = new ObjectStructure();
        client.attach(new Man("Ming"));
        client.attach(new Women("Hong"));

        Success success = new Success();
        client.disPlay(success);

        Fail fail = new Fail();
        client.disPlay(fail);
    }
}
```

![visitor](../../.gitbook/assets/visitor.png) 解读：在被访问的类Person里面加一个对外提供接待访问者的接口accept\(\)。

#### 1.3.2 访问者模式使用

### 1.4 迭代器模式\(Iterator Pattern\)

介绍：提供一种方法顺序访问一个聚合对象中各个元素, 而又无须暴露该对象的内部表示。 使用场景： 1. 访问一个聚合对象的内容而无须暴露它的内部表示。 2. 需要为聚合对象提供多种遍历方式。 3. 为遍历不同的聚合结构提供一个统一的接口。

#### 1.4.1示例

```text
/**
 * 专业
 */
public class Department {

    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Department(String name) {
        this.name = name;
    }
}

/**
 * 学院
 */
public interface College {

    String getName();

    void addDepartment(String name);

    Iterator iterator();
}

public class ComputerCollege implements College {

    Department[] departments = new Department[5];
    // 当前数组的对象个数
    int num = 0;

    @Override
    public String getName() {
        return "计算机学院";
    }

    // 初始化数据
    public ComputerCollege() {
        addDepartment("Java");
        addDepartment("Spring");
        addDepartment("数据结构");
    }

    @Override
    public void addDepartment(String name) {
        Department department = new Department(name);
        departments[num] = department;
        num += 1;
    }

    @Override
    public Iterator iterator() {
        return new ComputerCollegeIterator(departments);
    }
}

public class InfoCollege implements College {

    List<Department> departments = new ArrayList<>();

    @Override
    public String getName() {
        return "信息工程学院";
    }

    public InfoCollege() {
        addDepartment("信息安全");
        addDepartment("网络安全");
        addDepartment("服务器安全");
    }

    @Override
    public void addDepartment(String name) {
        Department department = new Department(name);
        departments.add(department);
    }

    @Override
    public Iterator iterator() {
        return new InfoCollegeIterator(departments);
    }
}

public class ComputerCollegeIterator implements Iterator{

    // 需要知道存放的形式 Arrays[]
    Department[] departments;
    // 遍历的位置
    int position = 0;

    public ComputerCollegeIterator(Department[] departments) {
        this.departments = departments;
    }

    @Override
    public boolean hasNext() {
        if (position >= departments.length || departments[position] == null){
            return false;
        }
        return true;
    }

    @Override
    public Object next() {
        Department department = departments[position];
        position += 1;
        return department;
    }

    @Override
    public void remove() {

    }
}

public class InfoCollegeIterator implements Iterator{

    // 需要知道存放的形式 List
    List<Department> departments;
    // 遍历的位置
    int position = -1;

    public InfoCollegeIterator(List<Department> departments) {
        this.departments = departments;
    }

    @Override
    public boolean hasNext() {
        if (position >= departments.size() - 1){
            return false;
        }
        return true;
    }

    @Override
    public Object next() {
        position += 1;
        return  departments.get(position);
    }

    @Override
    public void remove() {

    }
}

public class OutPut {

    // 学院
    List<College> collegeList;

    public OutPut(List<College> collegeList) {
        this.collegeList = collegeList;
    }

    public void printCollege(){
        Iterator<College> iterator = collegeList.iterator();
        while (iterator.hasNext()){
            College next = iterator.next();
            System.out.println(next.getName());
            printDepartment(next.iterator());
        }
    }

    /**
     * 通过迭代器进行输出
     * @param iterator
     */
    public void printDepartment(Iterator iterator){
        while (iterator.hasNext()){
            Department next = (Department)iterator.next();
            System.out.println(next.getName());
        }
    }
}

public class Client {

    public static void main(String[] args) {
        List<College> colleges = new ArrayList<>();
        ComputerCollege computerCollege = new ComputerCollege();
        InfoCollege infoCollege = new InfoCollege();
        colleges.add(computerCollege);
        colleges.add(infoCollege);

        OutPut outPut = new OutPut(colleges);
        outPut.printCollege();
    }
}
```

![iterator](../../.gitbook/assets/iterator.png) 解读：InfoCollegeIterator和ComputerCollegeIterator通过实现Iterator，来完成用统一的迭代器进行输出打印。

College和University通过聚合的方式组合起来，可以很方便的增加和删除，而叶子节点是最小的节点，不需要增加和删除。需要注意的是这里的College和University继承了同一个抽象类，需具有相同的部分。

#### 1.4.2 迭代器模式使用

JDK的ArrayList

### 1.5 观察者模式\(Observer Pattern\)

介绍：定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。 使用场景： 1. 一个抽象模型有两个方面，其中一个方面依赖于另一个方面。将这些方面封装在独立的对象中使它们可以各自独立地改变和复用。 2. 一个对象的改变将导致其他一个或多个对象也发生改变，而不知道具体有多少对象将发生改变，可以降低对象之间的耦合度。 3. 一个对象必须通知其他对象，而并不知道这些对象是谁。 4. 需要在系统中创建一个触发链，A对象的行为将影响B对象，B对象的行为将影响C对象……，可以使用观察者模式创建一种链式触发机制。

#### 1.5.1示例

```text
/**
 * 观察者
 */
public interface Observer {

    void update(Wether wether);
}

public class SinaObserver implements Observer {

    private Wether wether;

    @Override
    public void update(Wether wether) {
        this.wether = wether;
        display();
    }

    public void display(){
        System.out.println(wether.toString());
    }
}

public class Wether {

    private float temperature;

    private float pressure;

    private float humidity;

    public Wether(float temperature, float pressure, float humidity) {
        this.temperature = temperature;
        this.pressure = pressure;
        this.humidity = humidity;
    }

    @Override
    public String toString() {
        return "Wether{" +
                "temperature=" + temperature +
                ", pressure=" + pressure +
                ", humidity=" + humidity +
                '}';
    }
}

public interface Subject {

    /**
     * 订阅
     */
    void registerObservers(Observer observer);

    /**
     * 取消
     */
    void removeObservers(Observer observer);

    /**
     * 通知
     */
    void notifyObservers();
}

public class WetherData implements Subject{

    private Wether wether;

    private List<Observer> observers = new ArrayList<>();

    public WetherData(Wether wether) {
        this.wether = wether;
    }

    /**
     * 更新时通知
     * @param wether
     */
    public void update(Wether wether){
        this.wether = wether;
        notifyObservers();
    }

    @Override
    public void registerObservers(Observer observer) {
        observers.add(observer);
    }

    @Override
    public void removeObservers(Observer observer) {
        if (observers.contains(observer)){
            observers.remove(observer);
        }
    }

    @Override
    public void notifyObservers() {
        observers.forEach(observer -> {
            observer.update(wether);
        });
    }
}

public class Client {

    public static void main(String[] args) {
        WetherData wetherData = new WetherData(new Wether(3.2F, 4.4F, 5.6F));

        // 观察者
        SinaObserver sinaObserver = new SinaObserver();
        wetherData.registerObservers(sinaObserver);

        // 推送
        wetherData.notifyObservers();
        // 更新
        wetherData.update(new Wether(3F, 4F, 5F));
    }
}
```

![observer](../../.gitbook/assets/observer.png) 解读：Observe作为观察者，WetherData用集合管理观察者，可以进行订阅、取消、通知。

#### 1.5.2 观察者模式使用

JDK中的Observable

### 1.6 中介者模式\(Mediator Pattern\)

介绍：用一个中介对象来封装一系列的对象交互，中介者使各对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互。 使用场景： 1. 系统中对象之间存在比较复杂的引用关系，导致它们之间的依赖关系结构混乱而且难以复用该对象。 2. 想通过一个中间类来封装多个类中的行为，而又不想生成太多的子类。

#### 1.6.1示例

```text
/**
 * 中介者
 */
public abstract class Mediator {

    // 将中介者对象，加入到集合中
    public abstract void Register(User user);


    public abstract void getMessage(User user, String message);

    public abstract void sendMessage();
}

public class ConcreateMediator extends Mediator {

    // 用户集合
    private HashMap<String, User> userMap = new HashMap<>();
    // 消息集合
    private HashMap<String, String> messageMap = new HashMap<>();

    @Override
    public void Register(User user) {
        userMap.put(user.getName(), user);
    }

    @Override
    public void getMessage(User user, String message) {
        messageMap.put(user.getName(), message);
        System.out.println(LocalDate.now().format(DateTimeFormatter.ISO_DATE) + ": " + message);
    }

    @Override
    public void sendMessage() {
        messageMap.forEach((k, v) -> {System.out.println(v);});
    }
}


public abstract class User {

    private Mediator mediator;
    private String name;

    public User(Mediator mediator, String name) {
        this.mediator = mediator;
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public Mediator getMediator() {
        return mediator;
    }

    public abstract void sendMessage(String message);
}

public class XiaoHong extends User {

    public XiaoHong(Mediator mediator, String name) {
        super(mediator, name);
        mediator.Register(this);
    }

    @Override
    public void sendMessage(String message) {
        this.getMediator().getMessage(this, message);
    }
}

public class XiaoMing extends User {

    public XiaoMing(Mediator mediator, String name) {
        super(mediator, name);
        mediator.Register(this);
    }

    @Override
    public void sendMessage(String message) {
        this.getMediator().getMessage(this, message);
    }
}

public class Client {

    public static void main(String[] args) {
        Mediator mediator = new ConcreateMediator();

        User xiaoming = new XiaoMing(mediator, "xiaoming");
        User xiaohong = new XiaoHong(mediator, "xiaohong");

        xiaoming.sendMessage("Hi, I am xiaoming!");
        xiaohong.sendMessage("Hi, I am xiaohong!");
    }
}
```

![mediator](../../.gitbook/assets/mediator.png) 解读：Mediator作为中介者负责消息的接收和转发

#### 1.6.2 中介者模式使用

### 1.7 备忘录模式\(Memento Pattern\)

介绍：在不破坏封装性的前提下，捕获一个对象的内部状态，**并在该对象之外保存这个状态。** 使用场景： 1. 需要保存/恢复数据的相关状态场景。 2. 提供一个可回滚的操作。 3. 为了节约内存，可使用原型模式+备忘录模式。

#### 1.7.1示例

```text
/**
 * 需要保存状态的对象
 */
public class Originator {
    // 状态信息
    private String state;

    // 保存一个状态对象
    public Memento saveStateMemento(){
        return new Memento(state);
    }

    // 通过备忘录对象，恢复状态
    public void getStateFromMemento(Memento memento){
        state = memento.getState();
    }

    public String getState() {
        return state;
    }

    public void setState(String state) {
        this.state = state;
    }
}


/**
 * 备忘录对象，负责保存状态
 */
public class Memento {

    private String state;

    public String getState() {
        return state;
    }

    public Memento(String state) {
        this.state = state;
    }
}

/**
 * 守护者对象，负责保存多个备忘录对象
 */
public class Caretaker {

    private List<Memento> mementoList = new ArrayList<>();

    public void add (Memento memento){
        mementoList.add(memento);
    }

    public Memento get(int index){
        return mementoList.get(index);
    }
}

public class Client {

    public static void main(String[] args) {
        Originator originator = new Originator();
        Caretaker caretaker = new Caretaker();

        originator.setState("状态#1 攻击力 100");
        caretaker.add(originator.saveStateMemento());

        originator.setState("状态#2 攻击力 80");
        caretaker.add(originator.saveStateMemento());

        originator.setState("状态#3 攻击力 50");
        caretaker.add(originator.saveStateMemento());

        System.out.println("当前状态：" + originator.getState());
        originator.getStateFromMemento(caretaker.get(0));
        System.out.println("恢复状态：" + originator.getState());
    }
}
```

![memento](../../.gitbook/assets/memento.png) 解读：

#### 1.7.2 备忘录模式使用

### 1.8 解释器模式\(Interpreter Pattern\)

介绍：给定一个语言，定义它的文法表示，并定义一个解释器，这个解释器使用该标识来解释语言中的句子。 使用场景： 1. 以将一个需要解释执行的语言中的句子表示为一个抽象语法树。 2. 一些重复出现的问题可以用一种简单的语言来进行表达。 3. 一个简单语法需要解释的场景。 4. 可利用场景比较少，JAVA 中如果碰到可以用 expression4J 代替。

#### 1.8.1示例

```text
/**
 * 抽象表达式
 */
public interface Expression {

    int getValue();
}

public abstract class OperatorExpression implements Expression{

    // 左值
    protected Expression left;
    // 右值
    protected Expression right;

    public OperatorExpression(Expression left, Expression right) {
        this.left = left;
        this.right = right;
    }

    public abstract int getValue();
}

public class SubExpression extends OperatorExpression {

    public SubExpression(Expression left, Expression right) {
        super(left, right);
    }

    @Override
    public int getValue() {
        return left.getValue() - right.getValue();
    }
}

public class AddExpression extends OperatorExpression {

    public AddExpression(Expression left, Expression right) {
        super(left, right);
    }

    @Override
    public int getValue() {
        return left.getValue() + right.getValue();
    }
}

public class VarExpression implements Expression {

    private String value;

    public VarExpression(String value) {
        this.value = value;
    }

    @Override
    public int getValue() {
        return Integer.valueOf(this.value);
    }
}

/**
 * 常量
 */
public interface OperatorConstant {

    char ADD = '+';

    char SUB = '-';
}

public class ExpressionParser {

    private Expression expression;

    public Expression parseExpression(String expStr){
        Stack<Expression> stack = new Stack<>();
        // 拆分表达式
        char[] charArray = expStr.toCharArray();

        Expression left = null;
        Expression right = null;

        // 1+3-2
        for (int i = 0; i < charArray.length; i++){
            switch (charArray[i]){
                case OperatorConstant.ADD :
                    // 先取出左边
                    left = stack.pop();
                    // 取出右边
                    right = new VarExpression(String.valueOf(charArray[++i]));
                    // 运算后push
                    stack.push(new AddExpression(left, right));
                    break;
                case OperatorConstant.SUB :
                    left = stack.pop();
                    right = new VarExpression(String.valueOf(charArray[++i]));
                    stack.push(new SubExpression(left, right));
                    break;
                default :
                    // 如果是变量，就创建VarExpression对象，并push
                    stack.push(new VarExpression(String.valueOf(charArray[i])));
                    break;
            }
        }
        // 当遍历完整个charArray后，stack最后得到的Expression
        this.expression = stack.pop();
        return expression;
    }
}

public class Client {

    public static void main(String[] args) throws IOException {
        String context = getExpStr();
        ExpressionParser expressionParser = new ExpressionParser();
        Expression result = expressionParser.parseExpression(context);
        println(result.getValue());

    }

    public static String getExpStr() throws IOException{
        System.out.println("interpreter version 0.0.1");
        System.out.print(">>");
        InputStreamReader inputStreamReader = new InputStreamReader(System.in);
        return new BufferedReader(inputStreamReader).readLine();
    }

    public static void println(int result){
        System.out.print(">>");
        System.out.println(result);
    }
}
```

![interpreter](../../.gitbook/assets/interpreter.png) 解读：Expression为抽象的表达式，其子类根据不同字符进行不同解释。ExpressionParser作为表达式解释器的入口工具，最终返回一个表达式。

#### 1.8.2 解释器模式使用

Spring中的SpelExpressionParser

### 1.9 状态模式\(State Pattern\)

介绍：允许对象在内部状态发生改变时改变它的行为，对象看起来好像修改了它的类。 使用场景： 1. 行为随状态改变而改变的场景。 2. 条件、分支语句的代替者。

#### 1.9.1示例

```text
public enum ThreadStatusEnum {

    START("新建状态", 0),
    RUNNABLE("就绪状态", 1),
    RUNNING("运行状态", 2),
    BLOCKED("阻塞状态", 3),
    DEAD("死亡状态", 4);

    private String name;
    private int status;

    public String getName() {
        return name;
    }

    public int getStatus() {
        return status;
    }

    ThreadStatusEnum(String name, int status) {
        this.name = name;
        this.status = status;
    }
}

public interface ThreadState {

    void start(ThreadContext context);
    void getCPU(ThreadContext context);
    void suspend(ThreadContext context);
    void resume(ThreadContext context);
    void stop(ThreadContext context);
}

public abstract class AbstractThreadState implements ThreadState{

    protected int threadStatus = 0;

    public int getThreadStatus() {
        return threadStatus;
    }

    private static final RuntimeException EXCEPTION = new RuntimeException("cannot do it");

    /**
     * 抽象类默认实现
     */

    @Override
    public void start(ThreadContext context) { throw EXCEPTION; }

    @Override
    public void getCPU(ThreadContext context) { throw EXCEPTION; }

    @Override
    public void suspend(ThreadContext context) { throw EXCEPTION; }

    @Override
    public void resume(ThreadContext context) { throw EXCEPTION; }

    @Override
    public void stop(ThreadContext context) { throw EXCEPTION; }
}

public class NewState extends AbstractThreadState{

    public NewState() {
        this.threadStatus = ThreadStatusEnum.START.getStatus();
        System.out.println("当前线程处于：" + ThreadStatusEnum.START.getName());
    }

    /**
     * 开始
     */
    @Override
    public void start(ThreadContext context) {
        if (ThreadStatusEnum.START.getStatus() == context.getThreadStatus()){
            System.out.println("start() ->" + ThreadStatusEnum.RUNNABLE.getName());
            context.setState(new RunnableState());
        }else {
            System.out.println("cannot start");
        }
    }
}

public class RunnableState extends AbstractThreadState{

    public RunnableState() {
        this.threadStatus = ThreadStatusEnum.RUNNABLE.getStatus();
        System.out.println("当前线程处于：" + ThreadStatusEnum.RUNNABLE.getName());
    }

    /**
     * 获取CPU分配
     */
    public void getCPU(ThreadContext context) {
        if (ThreadStatusEnum.RUNNABLE.getStatus() == context.getThreadStatus()){
            System.out.println("getCPU() ->" + ThreadStatusEnum.RUNNING.getName());
            context.setState(new RunningState());
        }else {
            System.out.println("cannot getCPU");
        }
    }

}

public class RunningState extends AbstractThreadState{

    public RunningState() {
        this.threadStatus = ThreadStatusEnum.RUNNING.getStatus();
        System.out.println("当前线程处于：" + ThreadStatusEnum.RUNNING.getName());
    }

    /**
     * 暂停
     */
    public void suspend(ThreadContext context) {
        if (ThreadStatusEnum.RUNNING.getStatus() == context.getThreadStatus()){
            System.out.println("suspend() ->" + ThreadStatusEnum.BLOCKED.getName());
            context.setState(new BlockedState());
        }else {
            System.out.println("cannot suspend");
        }
    }

    /**
     * 停止
     */
    public void stop(ThreadContext context) {
        if (ThreadStatusEnum.RUNNING.getStatus() == context.getThreadStatus()){
            System.out.println("stop() ->" + ThreadStatusEnum.DEAD.getName());
            context.setState(new DeadState());
        }else {
            System.out.println("cannot stop");
        }
    }
}

public class BlockedState extends AbstractThreadState{

    public BlockedState() {
        this.threadStatus = ThreadStatusEnum.BLOCKED.getStatus();
        System.out.println("当前线程处于：" + ThreadStatusEnum.BLOCKED.getName());
    }

    /**
     * 恢复
     */
    public void resume(ThreadContext context) {
        if (ThreadStatusEnum.BLOCKED.getStatus() == context.getThreadStatus()){
            System.out.println("resume() ->" + ThreadStatusEnum.BLOCKED.getName());
            context.setState(new RunningState());
        }else {
            System.out.println("cannot resume");
        }
    }
}

public class DeadState extends AbstractThreadState{

    public DeadState() {
        this.threadStatus = ThreadStatusEnum.DEAD.getStatus();
        System.out.println("当前线程处于：" + ThreadStatusEnum.DEAD.getName());
    }
}

/**
 * 上下文 环境类
 */
public class ThreadContext{

    private AbstractThreadState state;

    // 读取状态
    public int getThreadStatus() { return state.getThreadStatus(); }

    // 设置状态
    public void setState(AbstractThreadState state) { this.state = state; }

    // 初始化状态
    public ThreadContext() { this.state = new NewState(); }

    public void start() { state.start(this); }

    public void getCPU() { state.getCPU(this); }

    public void suspend() { state.suspend(this); }

    public void resume() { state.resume(this); }

    public void stop() { state.stop(this); }
}

public class Client {

    public static void main(String[] args) {
        ThreadContext thread = new ThreadContext();
        thread.start();
        thread.getCPU();
        thread.suspend();
        thread.resume();
        thread.getCPU();
        thread.stop();
    }
}
```

![state](../../.gitbook/assets/state.png) 解读：ThreadContext是一个带有状态的类，当改变其状态时，其调用的方法也在发生改变。

#### 1.9.2 状态模式使用

### 2.0 策略模式\(Strategy Pattern\)

介绍：定义一系列的算法,把它们一个个封装起来, 并且使它们可相互替换。通过组合或聚合来解决继承问题。 说明：避免if...else语句。变化的部分则是不同的策略，但需要注意的是策略过多会导致类数目庞大。 使用场景： 1. 如果在一个系统里面有许多类，它们之间的区别仅在于它们的行为，那么使用策略模式可以动态地让一个对象在许多行为中选择一种行为。 2. 一个系统需要动态地在几种算法中选择一种。 3. 如果一个对象有很多的行为，如果不用恰当的模式，这些行为就只好使用多重的条件选择语句来实现。

#### 2.0.1示例

```text
/**
 * 策略类
 */
public interface Strategy {

    void handle();
}

public class ConcreteStrategyA implements Strategy{

    @Override
    public void handle() {
        System.out.println("strategy A");
    }
}

public class ConcreteStrategyB implements Strategy{

    @Override
    public void handle() {
        System.out.println("strategy B");
    }
}

public class Context {

    private Strategy strategy;

    public void setStrategy(Strategy strategy) {
        this.strategy = strategy;
    }

    public Context() {}

    public void handle(){
        // 也可用空对象模式
        if (null == strategy){
            System.out.println("default");
        }else {
        strategy.handle();
        }
    }
}

public class Client {

    public static void main(String[] args) {
        Strategy strategyA = new ConcreteStrategyA();
        Strategy strategyB = new ConcreteStrategyB();

        Context context = new Context();
        context.handle();


        context.setStrategy(strategyA);
        context.handle();

        context.setStrategy(strategyB);
        context.handle();

        Integer[] data = {1, 5, 2};
        Arrays.sort(data, (v1, v2) -> {
            // 策略
            if(v1.compareTo(v2) > 0){
                return -1;
            }else {
                return 1;
            }
        });
    }
}
```

![strategy](../../.gitbook/assets/strategy.png) 解读：Context聚合了抽象策略类，通过传入不同的策略，改变其结果。

#### 2.0.2 策略模式使用

Arrays的Comparator

### 2.1 职责链模式\(Chain of Responsibility Pattern\)

介绍：避免请求发送者与接收者耦合在一起，让多个对象都有可能接收请求，将这些对象连接成一条链，并且沿着这条链传递请求，直到有对象处理它为止。 使用场景： 1. 有多个对象可以处理同一个请求，具体哪个对象处理该请求由运行时刻自动确定。 2. 在不明确指定接收者的情况下，向多个对象中的一个提交一个请求。 3. 可动态指定一组对象处理请求。

#### 2.1.1示例

```text
/**
 * 自定义请求
 */
public class MyRequest {

    private String host;

    private int port;

    private int id;

    public int getPort() {
        return port;
    }

    public MyRequest(String host, int port, int id) {
        this.host = host;
        this.port = port;
        this.id = id;
    }
}

/**
 * 处理者
 */
public abstract class AbstractFilter {

    protected String name;

    // 下一个处理者，形成链式
    AbstractFilter filter;

    public AbstractFilter(String name) {
        this.name = name;
    }

    public void setFilter(AbstractFilter filter) {
        this.filter = filter;
    }

    public abstract void handler(MyRequest request);
}

public class ConcreteFilterA extends AbstractFilter{

    public ConcreteFilterA(String name) {
        super(name);
    }

    @Override
    public void handler(MyRequest request) {
        if (request.getPort() == 80){
            System.out.println("FilterA handler");
        }else {
            if (null == filter){
                filter = new ConcreteFilterB("FilterB");
            }
            filter.handler(request);
        }
    }
}

public class ConcreteFilterB extends AbstractFilter{

    public ConcreteFilterB(String name) {
        super(name);
    }

    @Override
    public void handler(MyRequest request) {
        if (request.getPort() == 4000){
            System.out.println("FilterB handler");
        }else {
            if (null == filter){
                filter = new ConcreteFilterB("FilterA");
            }
            filter.handler(request);
        }
    }
}

public class Client {

    public static void main(String[] args) {
        MyRequest request1 = new MyRequest("127.0.0.1", 80, 0);
        MyRequest request2 = new MyRequest("127.0.0.1", 4000, 1);

        AbstractFilter filter = new ConcreteFilterA("FilterA");

        filter.handler(request1);
        filter.handler(request2);

    }
}
```

![responsibilitychain](../../.gitbook/assets/responsibilitychain.png) 解读：AbstractFilter是一个抽象的处理者，处理者中又聚合了下一个处理者，当自身无法处理的时候，就可以交给下一个处理者处理。

#### 2.1 .2 职责链模式使用

SpingMVC中的HandlerExecutionChain+

