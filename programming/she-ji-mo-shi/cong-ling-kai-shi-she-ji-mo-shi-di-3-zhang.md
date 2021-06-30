---
title: 从零开始设计模式第3章-结构型模式(七种)
date: '2020-01-12T22:37:00.000Z'
categories: 从零开始设计模式
cover_img: images/common/4.jpg
feature_img: null
---

# 从零开始设计模式第3章-结构型模式\(七种\)

## 1.结构型模式\(7种\)

* 适配器模式\(Adapter Pattern\)
* 桥接模式\(Bridge Pattern\)
* 装饰器模式\(Decorator Pattern\)
* 组合模式\(Composite Pattern\)
* 外观模式\(Facade Pattern\)
* 享元模式\(Flyweight Pattern\)
* 代理模式\(Proxy Pattern\)

### 1.1 适配器模式\(Adapter Pattern\)

介绍：也叫变压器模式。将一个类的接口转换成客户希望的另外一个接口。适配器模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。\(适配器**继承或依赖**已有的对象，**实现**想要的目标接口。\) 使用场景： 1. 美国电器 110V，中国 220V，就要有一个适配器将 110V 转化为 220V。 2. JAVA JDK 1.1 提供了 Enumeration 接口，而在 1.2 中提供了 Iterator 接口，想要使用 1.2 的 JDK，则要将以前系统的 Enumeration 接口转化为 Iterator 接口，这时就需要适配器模式。 3. 在 LINUX 上运行 WINDOWS 程序。 4. JAVA 中的 jdbc。

#### 1.1.1示例

```text
/**
 * src
 */
public interface MediaPlayer {
    void play(String audioType, String fileName);
}

/**
 * target
 */
public interface AdvancedMediaPlayer {
    void playVlc(String fileName);
    void playMp4(String fileName);
}

public class Mp4Player implements AdvancedMediaPlayer {
    @Override
    public void playVlc(String fileName) {
        // nothing
    }

    @Override
    public void playMp4(String fileName) {
        System.out.println("Playing mp4 file. Name: "+ fileName);
    }
}

public class VlcPlayer implements AdvancedMediaPlayer {
    @Override
    public void playVlc(String fileName) {
        System.out.println("Playing vlc file. Name: "+ fileName);
    }

    @Override
    public void playMp4(String fileName) {
        // nothing
    }
}

/**
 * adapter
 */
public class MediaAdapter implements MediaPlayer {

    AdvancedMediaPlayer advancedMusicPlayer;

    public MediaAdapter(String audioType){
        if(audioType.equalsIgnoreCase("vlc") ){
            advancedMusicPlayer = new VlcPlayer();
        } else if (audioType.equalsIgnoreCase("mp4")){
            advancedMusicPlayer = new Mp4Player();
        }
    }

    /**
     * adapte the src to target
     * @param audioType
     * @param fileName
     */
    @Override
    public void play(String audioType, String fileName) {
        if(audioType.equalsIgnoreCase("vlc")){
            advancedMusicPlayer.playVlc(fileName);
        }else if(audioType.equalsIgnoreCase("mp4")){
            advancedMusicPlayer.playMp4(fileName);
        }
    }
}

/**
 * use the adapter
 */
public class AudioPlayer implements MediaPlayer {
    MediaAdapter mediaAdapter;

    @Override
    public void play(String audioType, String fileName) {

        // 播放 mp3 音乐文件的内置支持
        if(audioType.equalsIgnoreCase("mp3")){
            System.out.println("Playing mp3 file. Name: "+ fileName);
        }
        // mediaAdapter 提供了播放其他文件格式的支持
        else if(audioType.equalsIgnoreCase("vlc")
                || audioType.equalsIgnoreCase("mp4")){
            mediaAdapter = new MediaAdapter(audioType);
            mediaAdapter.play(audioType, fileName);
        }
        else{
            System.out.println("Invalid media. "+
                    audioType + " format not supported");
        }
    }
}

public class Client {
    public static void main(String[] args) {
        AudioPlayer audioPlayer = new AudioPlayer();

        audioPlayer.play("mp3", "beyond the horizon.mp3");
        audioPlayer.play("mp4", "alone.mp4");
        audioPlayer.play("vlc", "far far away.vlc");
        audioPlayer.play("avi", "mind me.avi");
    }
}
```

![factory](../../.gitbook/assets/adapter.png) 解读：AudioPlayer通过适配器MediaAdapter将原来的MediaPlayer转换成了AdvancedMediaPlayer。

#### 1.1.2 适配器模式使用

SpringMVC中的HandlerAdapter

### 1.2 桥接模式\(Bridge Pattern\)

介绍：将抽象部分与实现部分分离，使它们都可以独立的变化。 使用场景： 1. 如果一个系统需要在构件的抽象化角色和具体化角色之间增加更多的灵活性，避免在两个层次之间建立静态的继承联系，通过桥接模式可以使它们在抽象层建立一个关联关系。 2. 对于那些不希望使用继承或因为多层次继承导致系统类的个数急剧增加的系统，桥接模式尤为适用。 3. 一个类存在两个独立变化的维度，且这两个维度都需要进行扩展。

#### 1.2.1示例

```text
public interface Brand {
    void open();

    void close();

    void call();
}

public class XiaoMi implements Brand {
    @Override
    public void open() {
        System.out.println("Xiaomi open");
    }

    @Override
    public void close() {
        System.out.println("Xiaomi close");
    }

    @Override
    public void call() {
        System.out.println("Xiaomi call");
    }
}

public class Iphone implements Brand {
    @Override
    public void open() {
        System.out.println("Iphone open");
    }

    @Override
    public void close() {
        System.out.println("Iphone close");
    }

    @Override
    public void call() {
        System.out.println("Iphone call");
    }
}

/**
 * 桥接类
 */
public abstract class Phone {

    // 组合品牌
    private Brand brand;

    public Phone(Brand brand){
        super();
        this.brand = brand;
    }

    protected void open() {
        brand.open();
    }

    protected void close() {
        brand.close();
    }

    protected void call() {
        brand.call();
    }
}

public class FoledPhone extends Phone{

    public FoledPhone(Brand brand) {
        super(brand);
    }

    public void open() {
        super.open();
        System.out.println("折叠式手机");
    }

    public void close() {
        super.close();
        System.out.println("折叠式手机");
    }

    public void call() {
        super.call();
        System.out.println("折叠式手机");
    }
}

public class UpRightPhone extends Phone{

    public UpRightPhone(Brand brand) {
        super(brand);
    }

    public void open() {
        super.open();
        System.out.println("直立式手机");
    }

    public void close() {
        super.close();
        System.out.println("直立式手机");
    }

    public void call() {
        super.call();
        System.out.println("直立式手机");
    }
}


public class Client {

    public static void main(String[] args){
        Phone foledPhone = new FoledPhone(new Iphone());
        foledPhone.open();
        foledPhone.close();
        foledPhone.call();

        Phone upRightPhone = new UpRightPhone(new XiaoMi());
        upRightPhone.open();
        upRightPhone.close();
        upRightPhone.call();
    }
}
```

![bridge](../../.gitbook/assets/bridge.png) 解读：Phone作为桥接类，将不同的Brand通过聚合的方式组合起来。

#### 1.2.2 桥接模式使用

JDBC中的Driver

### 1.3 装饰器模式\(Decorator Pattern\)

介绍：动态地给一个对象添加一些额外的职责。就增加功能来说，装饰器模式相比生成子类更为灵活。 使用场景： 1. 扩展一个类的功能。 2. 动态增加功能，动态撤销。

#### 1.3.1示例

```text
/**
 * 整体抽象出来
 */
public abstract class Drink {

    public String desc;

    private float price = 0.0f;

    // 重点：计算费用，由子类实现
    public abstract float cost();

    public String getDesc() {
        return desc;
    }

    public void setDesc(String desc) {
        this.desc = desc;
    }

    public float getPrice() {
        return price;
    }

    public void setPrice(float price) {
        this.price = price;
    }
}

/**
 * 被装饰者
 */
public class Coffee extends Drink{

    @Override
    public float cost() {
        return super.getPrice();
    }
}

public class Espresso extends Coffee {
    public Espresso(){
        setDesc("意大利咖啡");
        setPrice(6.0f);
    }
}

public class LongBlack extends Coffee{

    public LongBlack(){
        setDesc("美式咖啡");
        setPrice(5.0f);
    }
}

/**
 * 装饰者
 */
public class Decorator extends Drink{

    // 被装饰者
    private Drink drink;

    public Decorator(Drink drink){
        this.drink = drink;
    }

    //组合
    @Override
    public float cost() {
        return super.getPrice() + drink.getPrice();
    }

    @Override
    public String getDesc() {
        return super.getDesc() + "+" + drink.getDesc();
    }
}

public class Milk extends Decorator {

    public Milk(Drink drink) {
        super(drink);
        setDesc("牛奶");
        setPrice(2.0f);
    }
}

public class Chocolate extends Decorator{

    public Chocolate(Drink drink) {
        super(drink);
        setDesc("巧克力");
        setPrice(3.0f);
    }
}


public class Client {

    public static void main(String[] args){
        Drink order = new Espresso();
        System.out.println(order.cost());
        System.out.println(order.getDesc());
        order = new Milk(order);
        System.out.println(order.cost());
        System.out.println(order.getDesc());
    }
}
```

![decorator](../../.gitbook/assets/decorator.png) 解读：Decorator作为装饰者，通过组合的方式装饰Coffee，Decorator装饰者和Coffee被装饰者两者拥有共同的父类。

#### 1.3.2 装饰器模式使用

JDK中的IO结构

### 1.4 组合模式\(Composite Pattern\)

介绍：将对象组合成**树形结构**以表示"部分-整体"的层次结构。组合模式使得用户对单个对象和组合对象的使用具有一致性。 使用场景： 1. 算术表达式包括操作数、操作符和另一个操作数，其中，另一个操作符也可以是操作数、操作符和另一个操作数。 2. 在 JAVA AWT 和 SWING 中，对于 Button 和 Checkbox 是树叶，Container 是树枝。

#### 1.4.1示例

```text
/**
 * 抽象类
 */
public abstract class OrganizationComponent {

    private String name;
    private String desc;

    protected void add(OrganizationComponent organizationComponent){
        throw new  UnsupportedOperationException();
    }

    protected void remove(OrganizationComponent organizationComponent){
        throw new  UnsupportedOperationException();
    }

    public abstract void print();

    public OrganizationComponent(String name, String desc) {
        this.name = name;
        this.desc = desc;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getDesc() {
        return desc;
    }

    public void setDesc(String desc) {
        this.desc = desc;
    }
}

public class University extends OrganizationComponent{

    // 聚合
    List<OrganizationComponent> organizationComponents = new ArrayList<>();

    public University(String name, String desc) {
        super(name, desc);
    }

    @Override
    protected void add(OrganizationComponent organizationComponent) {
        organizationComponents.add(organizationComponent);
    }

    @Override
    protected void remove(OrganizationComponent organizationComponent) {
        organizationComponent.remove(organizationComponent);
    }

    @Override
    public void print() {
        System.out.println(this.getName());
        for (OrganizationComponent organization : organizationComponents) {
            organization.print();
        }
    }
}

public class College extends OrganizationComponent{

    // 聚合
    List<OrganizationComponent> organizationComponents = new ArrayList<>();

    public College(String name, String desc) {
        super(name, desc);
    }

    @Override
    protected void add(OrganizationComponent organizationComponent) {
        organizationComponents.add(organizationComponent);
    }

    @Override
    protected void remove(OrganizationComponent organizationComponent) {
        organizationComponent.remove(organizationComponent);
    }

    @Override
    public void print() {
        System.out.println(this.getName());
        for (OrganizationComponent organization : organizationComponents) {
            organization.print();
        }
    }
}

/**
 * leaf叶子节点
 */
public class Department extends OrganizationComponent{


    public Department(String name, String desc) {
        super(name, desc);
    }

    @Override
    public void print() {
        System.out.println(this.getName());
    }
}

public class Client {

    public static void main(String args[]){
        OrganizationComponent university = new University("程序员大学", "万物皆明辨可惜");
        university.add(new College("计算机学院", "万物皆计算机"));
        university.add(new College("数学学院", "万物皆数学"));
        university.print();

        System.out.println("----------------------");
        OrganizationComponent college = new College("物理学院", "万物皆物理");
        college.add(new Department("物理一班", "一班一班"));
        college.add(new Department("物理二班", "二班二班"));
        university.add(college);
        university.print();

    }
}
```

![composite](../../.gitbook/assets/composite.png) 解读：College和University通过聚合的方式组合起来，可以很方便的增加和删除，而叶子节点是最小的节点，不需要增加和删除。需要注意的是这里的College和University继承了同一个抽象类，需具有相同的部分。

#### 1.4.2 组合模式使用

JDK中的HashMap

### 1.5 外观模式\(Facade Pattern\)

介绍：为子系统中的一组接口提供一个一致的界面，外观模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。 使用场景： 1. 为复杂的模块或子系统提供外界访问的模块。 2. 子系统相对独立。 3. 预防低水平人员带来的风险。

#### 1.5.1示例

```text
public interface Shape {
    void draw();
}

public class Square implements Shape {

    @Override
    public void draw() {
        System.out.println("Square::draw()");
    }
}

public class Circle implements Shape {

    @Override
    public void draw() {
        System.out.println("Circle::draw()");
    }
}

public class Rectangle implements Shape {

    @Override
    public void draw() {
        System.out.println("Rectangle::draw()");
    }
}

/**
 * 外观类
 */
public class ShapeMaker {
    private Shape circle;
    private Shape rectangle;
    private Shape square;

    public ShapeMaker() {
        // 实例化属性，可以用单例模式实例化
        circle = new Circle();
        rectangle = new Rectangle();
        square = new Square();
    }

    public void draw(){
        circle.draw();
        rectangle.draw();
        square.draw();
    }
}

public class Client {
    public static void main(String[] args) {
        ShapeMaker shapeMaker = new ShapeMaker();
        shapeMaker.draw();
    }
}
```

![facade](../../.gitbook/assets/facade.png) 解读：ShapeMaker作为外观类，通过组合的形式，把内部的细节都封装了。使用者只需要通过外观类进行调用即可。

#### 1.5.2 外观模式使用

Mybatis中的Configuration

### 1.6 享元模式\(Flyweight Pattern\)

介绍：也叫蝇量模式，运用共享技术有效地支持大量细粒度的对象。\(注意划分**外部状态和内部状态**，否则可能会引起线程安全问题。这些类必须有一个工厂对象加以控制。\) 使用场景： 1. JAVA 中的 String，如果有则返回，如果没有则创建一个字符串保存在字符串缓存池里面。 2. 数据库的数据池。

#### 1.6.1示例

```text
public abstract class WebSite {
    public abstract void used(User user);
}

public class ConcreateWebSite extends WebSite {

    private String type = "";

    public ConcreateWebSite(String type){
        this.type = type;
    }

    @Override
    public void used(User user) {
        System.out.println("user" + user.getName() + ", type:" + type);
    }
}

public class User {

    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public User(String name) {
        this.name = name;
    }
}

**
 * 享元池
 */
public class WebSiteFactory {

    private HashMap<String, ConcreateWebSite> pool = new HashMap<>();

    /**
     * 在享元池中取，如果没有则创建
     * @param type
     * @return
     */
    public WebSite getWebSiteByType(String type){
        if (!pool.containsKey(type)){
            pool.put(type, new ConcreateWebSite(type));
        }
        return pool.get(type);
    }

    public int getWebSitePoolSize(){
        return pool.size();
    }

}

public class Client {

    public static void main(String[] args) {
        WebSiteFactory webSiteFactory = new WebSiteFactory();
        webSiteFactory.getWebSiteByType("news");
        System.out.println(webSiteFactory.getWebSitePoolSize());
        webSiteFactory.getWebSiteByType("blog");
        System.out.println(webSiteFactory.getWebSitePoolSize());
        webSiteFactory.getWebSiteByType("shop");
        System.out.println(webSiteFactory.getWebSitePoolSize());

        WebSite news = webSiteFactory.getWebSiteByType("news");
        news.used(new User("A"));
        System.out.println(webSiteFactory.getWebSitePoolSize());
    }
}
```

![flyweight](../../.gitbook/assets/flyweight.png) 解读：通过整理内部状态，设计享元池，进行共享，减少了对象的创建。

#### 1.6.2 享元模式使用

Java中的String常量池、Integer.valueOf\(\)

### 1.7 代理模式\(Proxy Pattern\)

介绍：为其他对象提供一种代理以控制对这个对象的访问。代理模式有不同的形式，主要有三种：静态代理、动态代理\(JDK代理、接口代理\)、Cglib代理\(可以在内存动态的创建对象，而不需要实习接口，属于动态代理\)。 使用场景： 1. 远程代理。 2. 虚拟代理。 3. Copy-on-Write 代理。 4. 保护（Protect or Access）代理。 5. Cache代理。 6. 防火墙（Firewall）代理。 7. 同步化（Synchronization）代理。 8. 智能引用（Smart Reference）代理。

#### 1.7.1静态代理

```text
public interface TeacherDao {

    void teach();
}

public class TeacherDaoImpl implements TeacherDao {
    @Override
    public void teach() {
        System.out.println("teaching");
    }
}

/**
 * 代理对象
 * 静态代理，需要实现目标接口，聚合目标对象
 */
public class TeachDaoProxy implements TeacherDao{

    // 目标对象
    private TeacherDao target;

    public TeachDaoProxy(TeacherDao target){
        this.target = target;
    }

    @Override
    public void teach() {
        System.out.println("proxy start");
        target.teach();
        System.out.println("proxy end");
    }
}

public class Client {

    public static void main(String[] args) {
        TeachDaoProxy proxy = new TeachDaoProxy(new TeacherDaoImpl());
        // 通过代理对象，调用被代理对象的方法
        proxy.teach();
    }
}
```

![staticproxy](../../.gitbook/assets/staticproxy.png) 解读：TeachDaoProxy代理通过实现目标接口，聚合目标对象，进行静态代理。最后通过代理对象，调用被代理对象的方法。

#### 1.7.2 动态代理

```text
public interface TeacherDao {

    void teach();
}

public class TeacherDaoImpl implements TeacherDao {
    @Override
    public void teach() {
        System.out.println("teaching");
    }
}

/**
 * 代理对象
 * 动态代理，利用JDK的newProxyInstance方法，动态的在内存中构建代理对象
 * java.lang.reflect.Proxy\
 *
 * 根据传入的目标对象，利用反射机制，返回一个代理对象。然后通过代理对象，调用目标对象方法
 */
public class ProxyFactory {

    // 目标对象
    private Object target;

    public ProxyFactory(Object target){
        this.target = target;
    }

    public Object getProxyInstance(){
        /**
         * ClassLoader loader 指定当前目标对象使用的类加载器，获取加载器的方法固定
         * Class<?>[] interfaces 目标对象实现的接口类型，使用方向方式确认类型
         * InvocationHandler h 事情处理，执行目标对象的方法是，会触发事情处理器方法
         */
        ClassLoader loader = target.getClass().getClassLoader();
        Class<?>[] interfaces = target.getClass().getInterfaces();
        InvocationHandler h = (Object proxy, Method method, Object[] args) -> {
                System.out.println("反射机制调用目标对象的方法");
                Object invoke = method.invoke(target, args);
                System.out.println("JDK代理提交");
                return invoke;
        };
        return Proxy.newProxyInstance(loader, interfaces, h);
    }
}

public class Client {

    public static void main(String[] args) {
        TeacherDao target = new TeacherDaoImpl();
        TeacherDao proxy = (TeacherDao)new ProxyFactory(target).getProxyInstance();
        // 通过代理对象，调用目标对象的方法
        proxy.teach();
    }
}
```

![dynamicproxy](../../.gitbook/assets/dynamicproxy.png) 解读：ProxyFactory动态代理，利用JDK的newProxyInstance方法，动态的在内存中构建代理对象。最后通过代理对象，调用被代理对象的方法。

#### 1.7.3 Cglib代理

Cglib是一个强大的高性能的代码生成包，被许多框架使用，例如：Spring AOP。Cglib包的底层是通过字节码处理框架ASM来转换字节码并生产新的类。 **cglib是针对类来实现代理的，原理是对指定的目标类生成一个子类，并覆盖其中方法实现增强，但因为采用的是继承，所以不能对final修饰的类进行代理**

```text
public class TeacherDao {
    public void teach() {
        System.out.println("teaching");
    }
}

/**
 * MethodInterceptor 方法拦截器
 * cglib是针对类来实现代理的，原理是对指定的目标类生成一个子类，并覆盖其中方法实现增强，但因为采用的是继承，所以不能对final修饰的类进行代理
 */
public class ProxyFactory implements MethodInterceptor {

    Object target;

    public ProxyFactory(Object target){
        this.target = target;
    }

    /**
     * 返回target目标对象的代理对象
     * 调用代理对象的时候，会触发调用intercept()
     * @return
     */
    public Object getProxyInstance(){
        // 1.cglib增强类
        Enhancer enhancer = new Enhancer();
        // 2.设置父类
        enhancer.setSuperclass(target.getClass());
        // 3.设置回调函数
        enhancer.setCallback(this);
        // 4.创建子类对象，即代理对象
        return enhancer.create();
    }

    /**
     * 重写intercept方法，实现对被代理对象(目标对象)方法调用
     * @param o
     * @param method
     * @param objects 方法参数
     * @param methodProxy
     * @return
     * @throws Throwable
     */
    @Override
    public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
        System.out.println("cglib start");
        Object invoke = method.invoke(target, objects);
        System.out.println("cglib end");
        return invoke;
    }
}

public class Client {

    public static void main(String[] args) {
        TeacherDao target = new TeacherDao();
        TeacherDao proxy = (TeacherDao)new ProxyFactory(target).getProxyInstance();
        // 通过代理对象，调用目标对象的方法
        proxy.teach();
    }
}
```

![cglibproxy](../../.gitbook/assets/cglibproxy.png) 解读：ProxyFactory利用Cglib代理，当通过代理对象，调用目标对象时候，会触发MethodInterceptor方法拦截器，通过重写intercept方法，实现对被代理对象\(目标对象\)方法调用。

#### 1.7.4 对比

JDK代理：目标对象实现接口 Cglib代理：目标对象不需要实现接口

#### 1.7.5 代理模式使用

