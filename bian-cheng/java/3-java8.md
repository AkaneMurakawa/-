# Java8

[https://www.javazhiyin.com/13706.html](https://www.javazhiyin.com/13706.html)

## 一、内容

1. Lambda表达式
2. 函数式接口
3. 方法引用与构造器引用
4. Stream API
5. 接口中的默认方法与静态方法
6. 新时间日期API
7. 其他特性

## 二、介绍

从匿名内部类开始

```text
 // 以前
 Runnable t1 = new Runnable() {
     @Override
     public void run() {
         System.out.println("Run");
     }
 };

// 现在
Runnable t2 = () -> System.out.println("Lambda");
```

### 2.1 Lambda语法

格式

```text
参数列表 -> Lambda体

(parameters) -> expression 
    或 
(parameters) ->{ statements; }
```

#### 语法一：无参，并且无返回值

```text
() ->  System.out.println("Lambda");
```

#### 语法二：有一个参，并且无返回值

```text
(x) ->  System.out.println("Lambda");
```

若只有一个参数，小括号可以不写

```text
x ->  System.out.println("Lambda"); // 参数列表省略了()
```

#### 语法三：有两个以上参数，并且Lambda体中有多条语句

```text
Comparator<Integer> com = (x, y) -> {
    System.out.println("Lambda");
    return Integer.compare(x, y);
}
```

#### 语法四：若Lambda体中只有一条语句，return和大括号可以不写

```text
Comparator<Integer> com = (x, y) -> Integer.compare(x, y); // Lambda体中省略了return和大括号
```

#### 语法五：参数列表中的数据类型可以省略不写

因为JVM编译时会通过上下文推断数据类型，称之为 "类型推断"

```text
 (Integer x, Integer y) -> Integer.compare(x, y); // 参数列表中可以写数据类型
```

#### 总结

上联：左右遇一括号省

下联：左侧推断类型省

横批：能省则省

### 2.2 函数式接口

函数式接口：接口中只有一个抽象方法的接口，例如：Runnable，里面只有一个抽象的run\(\)方法。

可以使用@FunctionalInterface注解修饰，检查是否为函数式接口

```text
@FunctionalInterface // 检查是否为函数式接口
public interface MyPredicate<T> {
    void run();
}
```

#### 函数式编程

```text
public static void handler(MyPredicate predicate){
    predicate.run();
}

// 使用自定义的函数式接口
public static void main(String args[]){
    handler(() -> System.out.println("Lambda"));
}
```

#### Java8内置的四大核心函数式接口

我们每次自定义函数式接口太麻烦，因此Java8内置了四大核心函数式接口

```text
// 消费性接口
@FunctionalInterface
public interface Consumer<T> {
    void accept(T t);
}

// 供给型接口
@FunctionalInterface
public interface Supplier<T> {
    T get();
}

// 函数型接口
@FunctionalInterface
public interface Function<T, R> {
    R apply(T t);
}

// 断言型接口
@FunctionalInterface
public interface Predicate<T> {
    boolean test(T t);
}
```

**更多接口：package java.util.function;**

### 2.3 方法引用与构造器引用

#### 方法引用

若Lambda体中的**内容有方法已经实现了**，我们可以使用 "方法引用"的方式 \(可以理解为Lambda的另一种表现形式\)

语法格式

```text
三种语法格式：
对象::实例方法名
Consumer<String> con = System.out::println;

类::静态方法名
Comparator<Integer> com2 = Integer::compare; 

类::实例方法名
BiPredicate<String, String> bp2 = String::equals;
```

说明

```text
注意：这里的println方法的 参数列表和返回值 要和Consumer函数式接口的一致
@FunctionalInterface
public interface Consumer<T> {
    void accept(T t);
}

Comparator<Integer> com1 = (x, y) -> Integer.compare(x, y);
Comparator<Integer> com2 = Integer::compare; 

注意：这里要第一次参数x是方法的调用者，而第二个参数y是实例方法的参数时，才可以使用
BiPredicate<String, String> bp1 = (x, y) -> x.equals(y);
BiPredicate<String, String> bp2 = String::equals;
```

#### 构造器引用

```text
// 以前
Supplier<User> sup1 = () -> new User();
// 现在
Supplier<User> sup2 = User::new;
```

注意： 需要调用的构造器的参数列表要与函数式接口中的抽象方法的参数列表保持一致

#### 数组引用

```text
 // 以前
Function<Integer, String[]> fun1 = (x) -> new String[x];
fun1.apply(10);
// 现在
Function<Integer, String[]> fun2 = String[]::new;
fun2.apply(10);
```

### 2.4 Stream API

Stream是数据渠道，用于操作数据\(集合、数组等\)所生成的元素序列。Stream 是对集合（Collection）对象功能的增强，它专注于对集合对象进行各种非常便利、高效的聚合操作。

注意：

1. Stream不会存储元素
2. Stream不会改变源对象，返回一个新的Stream
3. Stream的操作可以 串行执行\(stream\) 或者 并行执行\(parallelStream\)

Stream 操作

1. 创建Stream
2. 中间操作Stream，对数据进行处理，只有最终操作的时候才会执行
3. 最终操作Stream

#### 创建Stream

```text
        List<User> list = new ArrayList<>();
        String[] strs = new String[3];

        // 创建Stream的四种方式
        // 1.通过集合提供的stream()
        Stream<User> stream1 = list.stream();
        // 2.通过Arrays.stream()
        Stream<String> stream2 = Arrays.stream(strs);
        // 3.通过Stream.of
        Stream<String> stream3 = Stream.of("a", "b");
        // 4. Stream.iterate创建无限流
        Stream<Integer> stream4 = Stream.iterate(0, x -> (x + 2));
        // 5.通过Stream.generate创建 无限流
        Stream<Double> stream5 = Stream.generate(() -> Math.random());
```

#### 中间操作Stream

中间操作不会执行任何操作，只有最终操作时，才会一次性全部执行

```text
List<User> list = new ArrayList<>();
list.add(new User("a", 15, "http://xxx.com/xxx"));
list.add(new User("b", 16, "http://xxx.com/xxx"));
list.add(new User("c", 17, "http://xxx.com/xxx"));
list.add(new User("d", 18, "http://xxx.com/xxx"));


// 流式编程
// filter
// limit
// skip
// distinct
// sorted
// map
// flatMap
// collect
List<String> nameList1 = list.stream()
    .filter(Objects::nonNull) // Predicate 过滤
    .limit(3) // 截断流
    .skip(2) // 跳过元素
    .distinct() // 去除重复
    .sorted() // 自然排序
    .sorted((x, y) -> {
        return x.getAge().compareTo(y.getAge());
    }) // 自定义排序
    .map(User::getName) // Function 将元素转换为其他形式
    .collect(Collectors.toList()); // 最终操作

List<List<User>> list2 = new ArrayList<>();
Stream<User> userStream = list2.stream()
    // <R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper);
    .flatMap(userList -> userList.stream()); // 将元素转换成另一个流，然后将所有流连接成一个流
```

#### 最终操作Stream

```text
// 最终操作
boolean b1 = list.stream()
    .allMatch(user -> user.getAge() > 18);// 所有匹配
boolean b2 = list.stream()
    .anyMatch(user -> user.getAge() > 18); // 至少匹配一个
boolean b3 = list.stream()
    .noneMatch(user -> user.getAge() > 18); // 检查是否没有匹配一个
Optional<User> first = list.stream()
    .findFirst(); // 返回第一个
Optional<User> any = list.stream()
    .filter(user -> user.getAge() > 18)
    .findAny(); // 返回任何一个
long count = list.stream()
    .filter(user -> user.getAge() > 18)
    .count(); // 返回元素的总个数
Optional<User> max = list.stream()
    .max((x, y) -> {
        return x.getAge().compareTo(y.getAge());
    }); // 返回最大的
Optional<User> min = list.stream()
    .min((x, y) -> {
        return x.getAge().compareTo(y.getAge());
    }); // 返回最小的


// --------------------------------reduce-------------------------------------
List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5);
Integer sum = integers.stream()
    .reduce(0, (x, y) -> x + y); // reduce规约，将元素反复结合起来，得到一个值


// --------------------------------collect-------------------------------------
integers.stream()
    .collect(Collectors.toList()); // collect 收集
integers.stream()
    .collect(Collectors.toSet()); // 收集
integers.stream()
    .collect(Collectors.toCollection(HashSet::new)); // 收集
integers.stream()
    .collect(Collectors.counting()); // 收集 counting
integers.stream()
    .collect(Collectors.averagingInt(Integer::intValue)); // 收集 averagingInt
integers.stream()
    .collect(Collectors.maxBy(Integer::compare)); // 收集 maxBy
list.stream()
    .collect(Collectors.groupingBy(User::getName)); // 收集 groupingBy
list.stream()
    .collect(Collectors.partitioningBy(user -> user.getAge() > 18)); // 收集 partitioningBy
list.stream()
    .map(User::getName)
    .collect(Collectors.joining(",")); // 收集 joining
```

### 2.5 并行流

#### Fork/Join

将一个大人物，进行拆分\(fork\)成若干个小任务，拆到不能再拆位置。最后将一个个小人物运算的结果进行汇总\(Join\)

工作窃取 - 双端队列

```text
list.parallelStream();
LongStream.rangeClosed(0, 10000000000L)
    .parallel() // 并行
    .reduce(0, Long::sum);
```

### 2.6 Optional

```text
User user = new User("b", 16, "http://xxx.com/xxx");
Optional<User> userOptional = Optional.of(user);// of 构造方法
User user1 = userOptional.get(); // 获取内容

Optional<Object> empty = Optional.empty(); // 创建一个空的Optional示例
Optional<User> optional = Optional.ofNullable(user); // 有则创建一个Optional示例，否则创建一个空的Optional示例

userOptional.isPresent(); // 是否存在
User c = userOptional.orElse(new User("c", 16, "http://xxx.com/xxx")); // 有值返回值，否则返回传入的值
userOptional.orElseGet(() -> new User("d", 16, "http://xxx.com/xxx"));
```

### 2.7 接口默认方法和静态方法

```text
public interface MyInterface {

    default void run(){
        System.out.println("默认方法");
    }

    static void getTime(){
        System.out.println("静态方法");
    }
}
```

### 2.8 时间日期API

LocalDate、LocalTime、LocalDateTime、ZonedDateTime、Instant

```text
LocalDateTime ldt = LocalDateTime.now(); // 获取当前时间
        LocalDateTime dateTime = LocalDateTime.of(2020, 6, 27, 12, 25, 0);
        dateTime.plusMonths(1); // 加一个月

        Instant start = Instant.now(); // 时间戳
        Instant end = Instant.now();
        long seconds = Duration.between(start, end).toMillis(); // 时间间隔，毫秒
        start.atOffset(ZoneOffset.ofHours(8));


        LocalDate now = LocalDate.now();
        LocalDate date = LocalDate.of(2020, 6, 27);
        Period.between(now, date).getDays(); // 日期间隔

        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy年MM月dd日");
```

### 2.9 可重复注解和类型注解

```text
@Retention(RetentionPolicy.RUNTIME)
@Repeatable(MyAnnotations.class) // 标记为可重复注解
public @interface MyAnnotation {

    String value();
}


@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnnotations {

    MyAnnotation[] value();
}


// 示例
@MyAnnotation("1")
@MyAnnotation("2")
public class AnnotationTest {

    // 在java8之前，注解只能是在声明的地方所使用，比如类，方法，属性。
    // java8开始，注解可以应用在任何地方
    void show(@MyAnnotation("3") String value){

    }   
}
```

## 三、示例

### 3.1 集合中的对象，将对象的内容转换为单独的集合

```text
static class User{
    public String a;
    public String b;

    public User(String a, String b) {
        this.a = a;
        this.b = b;
    }
}
public static void main(String[] args) {
    System.out.println("Hello World");

    List<User> tests = new ArrayList<User>(){{
        add(new User("aa", "bb"));
        add(new User("cc", "dd"));
    }};

    List<String> strs = tests.stream().flatMap(s -> Stream.of(s.a, s.b)).collect(Collectors.toList());
    System.out.println(strs);
}
```

### 3.2 数据根据条件去重

```text
  /**
     * 数据根据条件去重
     * <p>例：LambdaKit.deduplication(lstOtask, Otask::getTaskNo)
     * @param list
     * @param function
     * @param <T>
     * @return
     */
    public static <T, U extends Comparable<? super U>> List<T> deduplication(List<T> list, Function<? super T, ? extends U> function) {
        if (CollectionUtils.isEmpty(list)) {
            return Lists.newArrayList();
        }
        return list.stream().collect(
                Collectors.collectingAndThen(Collectors.toCollection(() -> new TreeSet<>(Comparator.comparing(function))), ArrayList::new)
        );
    }
```

### 3.3 Lambdakit 工具类

```text
import com.google.common.collect.Lists;
import com.google.common.collect.Maps;
import com.google.common.collect.Multimap;
import com.google.common.collect.Multimaps;
import org.apache.commons.collections.CollectionUtils;
import java.util.*;
import java.util.function.Function;
import java.util.function.Predicate;
import java.util.function.ToDoubleFunction;
import java.util.function.ToIntFunction;
import java.util.stream.Collectors;

/**
 * 常用java8的lambda工具类
 */
public class LambdaKit {

    /**
     * List<T> 转为 Map<K,T>，过滤重复key值
     * <p>例：Lambdakit.listToMap(lstSku, OtaskSkuDTO::getSkuId)
     * @return
     */
    public static <K, T> Map<K, T> listToMap(Collection<T> list, Function<T, K> keyMapper) {
        if (CollectionUtils.isEmpty(list)) {
            return Maps.newHashMap();
        }
        return list.stream().collect(Collectors.toMap(keyMapper, Function.identity(), (key1, key2) -> key2));
    }

    public static <K,V,T> Map<K, V> listToMap(Collection<T> list, Function<T, K> keyMapper, Function<T, V> valueMapper) {
        if (CollectionUtils.isEmpty(list)) {
            return Maps.newHashMap();
        }
        return list.stream().collect(Collectors.toMap(keyMapper, valueMapper, (key1, key2) -> key2));
    }


    /**
     * 合计
     * @return
     */
    public static <T> Integer sum(Collection<T> list, ToIntFunction<? super T> mapper) {
        if (CollectionUtils.isEmpty(list)) {
            return 0;
        }
        return list.stream().mapToInt(mapper).sum();
    }

    /**
     * 合计
     * @return
     */
    public static <T> Double total(Collection<T> list, ToDoubleFunction<? super T> mapper) {
        if (CollectionUtils.isEmpty(list)) {
            return 0d;
        }
        return list.stream().mapToDouble(mapper).sum();
    }

    /**
     * 过滤
     * <p>例：LambdaKit.filter(lstOtask, task -> task.getStatus().equals(taskStatus))
     * @return
     */
    public static <T> List<T> filter(List<T> list, Predicate<? super T> predicate) {
        if (CollectionUtils.isEmpty(list)) {
            return Lists.newArrayList();
        }
        return list.stream().filter(predicate).collect(Collectors.toList());
    }

    /**
     * 数据根据条件去重
     * <p>例：LambdaKit.deduplication(lstOtask, Otask::getTaskNo)
     * @return
     */
    public static <T, U extends Comparable<? super U>> List<T> deduplication(List<T> list, Function<? super T, ? extends U> function) {
        if (CollectionUtils.isEmpty(list)) {
            return Lists.newArrayList();
        }
        return list.stream().collect(
                Collectors.collectingAndThen(Collectors.toCollection(() -> new TreeSet<>(Comparator.comparing(function))), ArrayList::new)
        );
    }

    /**
     * 转换集合属性
     * @return
     */
    public static <T, R> List<R> convert(Collection<T> list, Function<T, R> mapper) {
        if (CollectionUtils.isEmpty(list)) {
            return Lists.newArrayList();
        }
        return list.stream().map(mapper).collect(Collectors.toList());
    }
    /**
     * 获取唯一属性
     * @return
     */
    public static <T, R> List<R> convertUnique(Collection<T> list, Function<T, R> mapper) {
        if (CollectionUtils.isEmpty(list)) {
            return Lists.newArrayList();
        }
        return list.stream().map(mapper).distinct().collect(Collectors.toList());
    }

    /**
     * 按属性分组(相同属性Map.get->List)
     * @return
     */
    public static <K, T> Multimap<K, T> group(Collection<T> list, com.google.common.base.Function<? super T, K> keyFunction) {
        if (CollectionUtils.isEmpty(list)) {
            list = Lists.newArrayList();
        }
        return Multimaps.index(list, keyFunction);
    }

    /**
     * 查找符合条件的第一个对象，如果没有符合条件的对象，则返回{@code null}
     *
     * <p>例：LambdaKit.findFirst(lstOtask, task -> task.getStatus().equals(taskStatus))
     * @return
     */
    public static <T> T findFirst(List<T> list, Predicate<? super T> predicate) {
        if (CollectionUtils.isEmpty(list)) {
            return null;
        }

        list = list.stream().filter(predicate).collect(Collectors.toList());
        if (CollectionUtils.isEmpty(list)) {
            return null;
        }

        return list.get(0);
    }
}
```

