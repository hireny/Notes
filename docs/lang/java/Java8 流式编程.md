# 介绍

JDK 8 不止新增了 Lambda 表达式，还有 `Stream` 流 ，程序员通过 `Stream` 流来简化对数据的处理。其本质就是计算。

可以这么理解：流就是数据通道，用于操作数据源所生成的元素序列。

我们来熟悉一下 `Stream` 流：

```java
public class StringSorting {
    public static void main(String[] args) {
        Stream.of("Java", "Python", "C++",
                "C", "Shell", "Ruby",
                "Scala", "Groovy", "Kotlin",
                "Clojure", "Jython", "C#",
                "JavaScript", "SQL")
                .sorted()   // 自然序排序
                .forEach(s -> System.out.printf("%s ", s));  // 打印输出
    }
}
```

输出结果：

```shell
C C# C++ Clojure Groovy Java JavaScript Jython Kotlin Python Ruby SQL Scala Shell 
```

由上面的例子，我们可以得知使用 `Stream` 的步骤：

![Stream流式操作](./images/Stream操作)

1. 创建流。例如上面的 `Stream.of()`
2. 对流的中间操作。例如上面的 `sorted()`，在流中对数据进行排序。
3. 最终操作。例如上面的 `forEach()`，将流通过该方法进行打印。


# 流创建

流的创建主要有以下几种方式：

以下几种方式可以创建 `Stream` 流：

- `Arrays` 数组工具类提供的 `stream()` 静态方法。
- `Stream` 类中 `of()`、`generate()`、`iterate()`和 `empty()` 静态方法。
- `Stream` 类中的 `builder()` 方法。
- `Collection` 集合提供的 `stream()` 方法与 `parallelStream()` 方法。

还有其它的一些产生流的方法就不一一列举了。下面讲解一下上述创建 `Stream` 流的方法

### Arrays.stream() 静态方法

以下是 `Arrays` 关于产生流的方法 `stream()` 及重载方法：

```java
public class Arrays {
	...省略...
	public static <T> Stream<T> stream(T[] array) {...}
    public static <T> Stream<T> stream(T[] array, int startInclusive, int endExclusive) {...}
    public static IntStream stream(int[] array) {...}
    public static IntStream stream(int[] array, int startInclusive, int endExclusive) {...}
    public static LongStream stream(long[] array) {...}
    public static LongStream stream(long[] array, int startInclusive, int endExclusive) {...}
    public static DoubleStream stream(double[] array) {...}
    public static DoubleStream stream(double[] array, int startInclusive, int endExclusive) {...}
    ...省略...
}
```

由以上方法可知，通过重载形式，可以满足我们调用的基本类型与泛型的数组转化为 `Stream` 流。

下面是使用 `Arrays.stream()` 来将数组转换为流的例子：

```java
public class ArraysStream {
    public static void main(String[] args) {
        Arrays.stream(new double[] {3.1415926, 9.8, 3.3333})
                .forEach(System.out::println);
    }
}
```

### Stream 的静态方法

`Stream` 流中共有 `of()`、`generate()` 和 `iterate()` 三个静态方法。

我们先看一下 `Stream.of()` 静态方法的源码：

```java
public interface Stream<T> extends BaseStream<T, Stream<T>> {
	...省略...
    @SafeVarargs
    @SuppressWarnings("varargs") // Creating a stream from an array is safe
    public static<T> Stream<T> of(T... values) {
        return Arrays.stream(values);
    }
    ...省略...
}
```

由以上源码可知，使用 `Stream.of()` 静态方法，是将可变元素传递到 `Arrays` 数组工具类的静态方法 `stream()` 去转换为流，其实质还是调用 `Arrays.stream()` 方法。

下面我们来看一下，使用 `Stream.of()` 来将可变参数数组转化为流的例子：

```java
public class StreamOf {
    public static void main(String[] args) {
    	// 将一组数字转换为流
        Stream.of(9.8, 3.1415926, 3.33333)
                .forEach(System.out::println);
    }
}
```

`Stream.of()` 静态方法的源码和例子如上所述。·

`Stream` 还有产生无限元素的静态方法用于产生流，它就是`generate()` 静态方法：

```java
public interface Stream<T> extends BaseStream<T, Stream<T>> {
	...省略...
    public static<T> Stream<T> generate(Supplier<? extends T> s) {...}
    ...省略...
}
```

使用 `generate()` 方法创建的流，长度是无限的，它的参数是 `Supplier` 函数接口。

```java
public class StreamGenerate {
    public static void main(String[] args) {
        Stream.generate(Math::random)
                .limit(10)
                .forEach(i -> System.out.printf("%f ", i));
    }
}
```

输出结果：

```shell
0.792192 0.625073 0.983115 0.372405 0.294238 0.790635 0.688823 0.238186 0.096708 0.434963 
```

我们在看另一个创建无限流的静态方法 `Stream.iterate()` ：

```java
public interface Stream<T> extends BaseStream<T, Stream<T>> {
	...省略...
    public static<T> Stream<T> iterate(final T seed, final UnaryOperator<T> f) {...}
    
    public static<T> Stream<T> iterate(T seed, Predicate<? super T> hasNext, UnaryOperator<T> next) {}
    ...省略...
}
```

上述两个静态方法都是 `iterate` 用来创建无限流的，与 `generate` 方法不同的是，它是通过函数`f`迭代给指定的种子而产生无限连续有序的`Stream`。

```java
public class StreamIterate {
    int x = 1;
    Stream<Integer> numbers() {
        return Stream.iterate(0, i -> {
            int result = x + i;
            x = i;
            return result;
        });
    }
    public static void main(String[] args) {
        new StreamIterate().numbers()
                .skip(20)           // 过滤前 20 个
                .limit(10)          // 然后取 10 个
                .forEach(i -> System.out.printf("%d ", i));
    }
}
```

输出结果：

```shell
6765 10946 17711 28657 46368 75025 121393 196418 317811 514229 
```

`Stream` 类中还有一个创建不含任何元素的 `Stream` 流：

```java
public interface Stream<T> extends BaseStream<T, Stream<T>> {
	...省略...
    public static<T> Stream<T> empty() {...}
    ...省略...
}
```

创建空流的例子如下：

```java
public class StreamEmpty {
    public static void main(String[] args) {
        Stream<Object> empty = Stream.empty();
    }
}
```

### Stream的构造器模式

`Stream` 类还可以使用构建器 `builder()` 方法创建流。

```java
public class StreamBuilder {
    public static void main(String[] args) {
        Stream.builder()
                .add("Jan").add("Feb").add("Mar")
                .add("Apr").add("May").add("Jun")
                .build()
                .forEach(s -> System.out.printf("%s ", s));
    }
}
```

输出结果：

```shell
Jan Feb Mar Apr May Jun 
```

### Collection系列

Java 8 中，扩展了 `Collection` 接口，添加了两个默认方法，来创建流：

```java
public interface Collection<E> extends Iterable<E> {
	...省略...
    default Stream<E> stream() {...}
    default Stream<E> parallelStream() {...}
}
```

上面的两个默认方法，`stream()` 是转化为顺序流，`paralletlStream()` 转换为并行流。

下面我们看一下两个默认方法的使用例子：

```java
public class ListStream {
    public static void main(String[] args) {
        String[] months = {"Jan", "Feb", "Mar", "Apr", "May",
                "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec", "Jun", "Jun", "Feb", "Jan"};
        // 集合创建
        List<String> list = Arrays.asList(months);
        list.stream().forEach(s -> System.out.format("%s ", s));
    }
}
```

输出结果：

```shell
Jan Feb Mar Apr May Jun Jul Aug Sep Oct Nov Dec 
```

但将 `List` 对象的 `stream()` 方法变换为 `parallelStream()` 方法后，

输出结果：

```java
Aug Jul Jun Feb Dec Oct Nov Apr Mar May Jan Sep
```

从上面我们可以看出，`stream()` 与 `parallelStream()` 的区别：

`stream()` 是转化为顺序流，它使用主线程，是单线程的；`parallelStream()` 是转化为并行流，它是多个线程同时运行的。因此，`stream()` 是按顺序输出的，而`parallelStream()` 不是。

那我们如何将 `Map` 关系映射表转化为流呢？

看下面的例子：

```java
public class MapStream {
    public static void main(String[] args) {
        Map<String, Integer> users = new HashMap<>();
        users.put("小赵", 18);
        users.put("小钱", 29);
        users.put("小孙", 20);
        users.put("小李", 29);

        users.entrySet().stream().forEach(entry -> System.out.format("%s ", entry));
        System.out.println();
        users.keySet().stream().forEach(key -> System.out.format("%s ", key));
        System.out.println();
        users.values().stream().forEach(value -> System.out.format("%d ", value));
    }
}
```

输出结果：

```shell
小孙=20 小李=29 小钱=29 小赵=18 
小孙 小李 小钱 小赵 
20 29 29 18 
```

从上面的例子就可得知，我们得到 `Map` 的 `Entity` 集合，`Key` 的集合和`Value`的集合，在将它们转化为流去处理。


# 中间操作

中间操作就是对上述创建的流进行处理，处理完返回的还是流，以便于其它操作。

我们来看一下都有什么中间操作：`peek()`、`sorted()`、`unordered()`、`distinct()`、`filter()`、`map()`、`flatMap()`、`limit()`、`skip()` 等。下面我们来详细介绍这些操作。


### peek

`peek` 操作的方法如下：

```java
Stream<T> peek(Consumer<? super T> action);
```

主要目的是接收一个 `Consumer` 函数提供的逻辑去对流中的元素进行操作。

`peek()` 操作的目的是帮助调试。它允许你无修改地查看流中的元素。代码示例：

```java
public class Peeking {
    public static void main(String[] args) {
        Stream.of("Hello", "Hello World!", "I'm hello")
                .peek(System.out::println)
                .collect(Collectors.toList());
    }
}
```

输出结果：

```shell
Hello
Hello World!
I'm hello
```

再看下一个例子：

```java
public class Peeking {
    public static void main(String[] args) {
        List<String> list  = Stream.of("Hello", "Hello World!", "I'm hello")
                .map(String::toUpperCase)
                .collect(Collectors.toList());
        System.out.println(list);

        list = Stream.of("Hello", "Hello World!", "I'm hello")
                .peek(String::toUpperCase)
                .collect(Collectors.toList());
        System.out.println(list);
    }
}
```

输出结果：

```shell
[HELLO, HELLO WORLD!, I'M HELLO]
[Hello, Hello World!, I'm hello]
```

可以看出使用`map()` 操作对元素进行了转换，而使用 `peek()` 并没有对元素进行转换。

因此，`peek()` 一般用于不想改变流中元素本身的类型或者只想操作元素的内部状态时；而 `map()` 则用于改变流中元素本身类型，即从元素中派生出另一种类型的操作。

`peek()` 适用于对 `Stream<T>` 中 泛型的某些属性进行批处理的时候使用。

### sorted

`sorted` 操作的方法如下：

```java
Stream<T> sorted();
// 或者
Stream<T> sorted(Comparator<? super T> comparator);
```

`sorted()` 可以使用无参方法，也可以传入 `Comparator` 参数。代码示例：

```java
public class SortedComparator {
    public static void main(String[] args) throws Exception {
        FileToWords.stream("Cheese.dat")
                .skip(10)
                .limit(10)
                .sorted(Comparator.reverseOrder())
                .map(w -> w + " ")
                .forEach(System.out::print);
    }
}
```

输出结果：

```shell
you what to the that sir leads in district And
```

`sorted()` 预设了一些默认的比较器。这里我们使用的是反转 "自然排序"。当然你也可以把 Lambda 函数作为参数传递给 `sorted()`。

### distinct

`distinct` 的方法如下：

```java
Stream<T> distinct();
```

它的作用主要是去重：

```java
public class DistinctOperator {
    public static void main(String[] args) {
        Stream.of(45, 31, 21, 98, 31, 55, 982, 45, 54)
                .distinct().forEach(n -> System.out.format("%d ", n));
    }
}
```

输出结果：

```shell
45 31 21 98 55 982 54 
```

### filter

`filter` 的方法如下：

```java
Stream<T> filter(Predicate<? super T> predicate);
```

该方法是过滤操作，把不想要的数据过滤掉，留下想要的的数据。会根据传入的 `Predicate` 函数，将不符合的数据过滤掉，留下符合 `Predicate` 函数的数据。

我们看下面的例子来熟悉一下 `filter()` 方法的使用：

```java
class Month {
    private String name;
    private int id;
    public Month(String name, int id) { this.name = name;this.id = id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getId() { return id; }
    public void setId(int id) { this.id = id; }
    @Override
    public String toString() {
        return "Month{" + "name='" + name + '\'' + ", id=" + id + '}';
    }
}
public class FilterOperator {
    public static void main(String[] args) {
        String[] strings = new String[]{"Jan", "Feb", "Mar", "Apr", "May",
                "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"};
        Month[] months = new Month[strings.length];
        for (int i = 0; i < strings.length; i++) {
            months[i] = new Month(strings[i], i+1);
        }
        Stream.of(months)
                .filter(s -> "J".equals(s.getName().substring(0, 1)))
                .forEach(System.out::println);
    }
}
```

输出结果：

```shell
Month{name='Jan', id=1}
Month{name='Jun', id=6}
Month{name='Jul', id=7}
```

从结果中可以看到，凡是首字母与 `J` 相等的对象都被打印出来了。

### map

`map` 的方法如下：

```java
<R> Stream<R> map(Function<? super T, ? extends R> mapper);
```

它接收一个 `Function` 函数，作用是将一个类型的对象转换为另一个类型的对象。

下面我们编写一个例子来看一下它的操作方式：

```java
class Student {
    private String name;
    private int age;
    public Student(String name, int age) { this.name = name;this.age = age; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
    @Override
    public String toString() {
        return "Student{" + "name='" + name + '\'' + ", age=" + age + '}';
    }
}
public class MapOperator {
    public static void main(String[] args) {
        String[] strings = new String[]{"小赵", "小钱", "小孙",
                "小李", "小周", "小吴", "小郑", "小王"};
        int[] ints = new int[] {20 , 18, 31, 28, 20, 25, 31, 20};
        Student[] students = new Student[strings.length];
        for (int i = 0; i < strings.length; i++) {
            students[i] = new Student(strings[i], ints[i]);
        }
        Stream.of(students).map(Student::getName).forEach(s -> System.out.printf("%s ", s));
        System.out.println();
        Stream.of(students).mapToInt(Student::getAge).forEach(i -> System.out.printf("%d ", i));
        System.out.println();
    }
}
```

输出结果：

```shell
小赵 小钱 小孙 小李 小周 小吴 小郑 小王 
20 18 31 28 20 25 31 20 
```

从上面的例子可知，第一个输出是从 `Student` 对象数组中将所有学生的姓名都获取出来并打印，第二个是打印年龄。

从第二个可以看出，源码已经封装好了转换为基本类型的 `mapToInt` 方法，返回的结果为 `IntStream`，其它所有关于 `map` 操作的源码如下：

```java
// 根据 `ToIntFunction` 函数，获取 `IntStream` 流
IntStream mapToInt(ToIntFunction<? super T> mapper);
// 根据 `ToLongFunction` 函数，获取 `LongStream` 流
LongStream mapToLong(ToLongFunction<? super T> mapper);
// 根据 `ToDoubleFunction` 函数，获取 `DoubleStream` 流
DoubleStream mapToDouble(ToDoubleFunction<? super T> mapper);
```

### flatMap

`flatMap` 的方法如下：

```java
<R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper);
```

该方法接收一个 `Function` 函数作为参数，将流中的每个值都转换成另一个流，然后把所有流连接成一个流，是扁平化操作。

例子：

```java
class Employee {
    private int id;
    private String name;
    private double salary;
    public Employee(int id, String name, double salary) { this.id = id;this.name = name;this.salary = salary; }
    public int getId() { return id; }
    public void setId(int id) { this.id = id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public double getSalary() { return salary; }
    public void setSalary(double salary) { this.salary = salary; }
    @Override
    public String toString() {
        return "Employee{" + "id=" + id + ", name='" + name + '\'' + ", salary=" + salary + '}';
    }
}
public class FlatMapOperator {
    public static void main(String[] args) {
        Employee[] employees1 = new Employee[] {
                new Employee(1, "小赵", 1500),
                new Employee(2, "小钱", 3500),
                new Employee(3, "小孙", 500),
                new Employee(4, "小李", 4500),
        };
        Employee[] employees2 = new Employee[] {
                new Employee(5, "小周", 9000),
                new Employee(6, "小吴", 6200),
                new Employee(7, "小郑", 1850),
                new Employee(8, "小王", 3210),
        };
        Stream.of(employees1, employees2).flatMap(e -> Arrays.stream(e)).forEach(System.out::println);
    }
}
```

输出结果：

```shell
Employee{id=1, name='小赵', salary=1500.0}
Employee{id=2, name='小钱', salary=3500.0}
Employee{id=3, name='小孙', salary=500.0}
Employee{id=4, name='小李', salary=4500.0}
Employee{id=5, name='小周', salary=9000.0}
Employee{id=6, name='小吴', salary=6200.0}
Employee{id=7, name='小郑', salary=1850.0}
Employee{id=8, name='小王', salary=3210.0}
```

从结果上看，`flatMap()` 将两个数组转换成流并合并在一起。

`flatMap()` 也有封装好的具体类型的方法，源码如下：

```java
IntStream flatMapToInt(Function<? super T, ? extends IntStream> mapper);
LongStream flatMapToLong(Function<? super T, ? extends LongStream> mapper);
DoubleStream flatMapToDouble(Function<? super T, ? extends DoubleStream> mapper);
```

### limit

`limit` 的方法如下：

```java
Stream<T> limit(long maxSize);
```

它的作用是根据 `maxSize` 参数的大小，截取流，使其元素个数不超过 `maxSize`。

我们给一个例子来熟悉 `limit()` 的使用：

```java
public class LimitOperator {
    public static void main(String[] args) {
        String[] strings = new String[]{"Jan", "Feb", "Mar", "Apr", "May",
                "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"};
        Stream.of(strings).limit(5).forEach(s -> System.out.printf("%s ", s));
    }
}
```

输出结果：

```shell
Jan Feb Mar Apr May 
```

### skip

`skip` 的方法如下：

```java
Stream<T> skip(long n);
```

跳过元素，返回一个扔掉了前 n 个元素的流。若流中元素不足 n 个，则返回一个空流。与 `limit` 互补。

```java
public class SkipOperator {
    public static void main(String[] args) {
        String[] strings = new String[]{"Jan", "Feb", "Mar", "Apr", "May",
                "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"};
        Stream.of(strings).skip(10).forEach(s -> System.out.printf("%s ", s));
    }
}
```

输出结果：

```java
Nov Dec
```

# 终端操作

终端操作就是为了结束流的中间操作，并返回结果。至此无法在继续往后传递流。

终端操作都有哪些操作呢？我们简单来看下：`forEach()`、`forEachOrdered()`、`toArray()`、`collect()`、`reduce()`、`min()`、`max()`、`count()`、`anyMatch()`、`allMatch()`、`noneMatch()`、`findFirst()`、`findAny()`等。下面详细介绍一下这些终端操作的使用。

### forEach

`forEach` 的方法如下：

```java
void forEach(Consumer<? super T> action);
```

主要是进行编译操作，我们传递进去一个 `Consumer` 函数，`Stream` 在内部进行迭代。

```java
public class ForEachOperator {
    public static void main(String[] args) {
        String[] strings = new String[]{"Jan", "Feb", "Mar", "Apr", "May",
                "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"};
        Stream.of(strings).forEach(s -> System.out.printf("%s ", s));
    }
}
```

### forEachOrdered

`forEachOrdered` 的方法如下：

```java
void forEachOrdered(Consumer<? super T> action);
```

它的作用与 `forEach()` 一样，但是在并行流上的区别较大

```java
public class ForEachOrderedOperator {
    public static void main(String[] args) {
        String[] strings = new String[]{"Jan", "Feb", "Mar", "Apr", "May",
                "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"};
        Stream.of(strings).parallel().forEach(s -> System.out.printf("%s ", s));
        System.out.println();
        Stream.of(strings).parallel().forEachOrdered(s -> System.out.printf("%s ", s));
    }
}
```

输出结果：

```java
Aug May Dec Oct Jun Feb Apr Nov Mar Jan Jul Sep 
Jan Feb Mar Apr May Jun Jul Aug Sep Oct Nov Dec 
```

从输出结果可知，使用并行流进行操作时，`forEachOrdered()` 会严格按照顺序进行输出，而`forEach()` 不会。

### toArray

`toArray` 的方法如下：

```java
Object[] toArray();
<A> A[] toArray(IntFunction<A[]> generator);
```

`toArray` 有两个方法，一个无参，一个传入

我们给出一个例子，来熟悉使用 `toArray()` 方法：

```java
public class ToArrayOperator {
    public static void main(String[] args) {
        List<String> strings = Arrays.asList("Jan", "Feb", "Mar", "Apr", "May",
                "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec");

        // 使用 toArray() 将流转化为数组
        Object[] objects = strings.stream()
                .filter(s -> "J".equals(s.substring(0, 1))).toArray();
        System.out.println(Arrays.toString(objects));

        // 使用 toArray() 将流转化为特定的数组
        String[] strings1 = strings.stream()
                .filter(s -> "A".equals(s.substring(0, 1))).toArray(size -> {
                    return new String[size];
                });
        System.out.println(Arrays.toString(strings1));

        // 上面的写法可以使用方法引用
        String[] strings3 = strings.stream()
                .filter(s -> "M".equals(s.substring(0, 1))).toArray(String[]::new);
        System.out.println(Arrays.toString(strings3));
    }
}
```

输出结果：

```shell
[Jan, Jun, Jul]
[Apr, Aug]
[Mar, May]
```

从结果上看，我们知道 `toArray()` 方法就是将流转换为数组。

### collect

`collect` 的方法如下：

```java
<R, A> R collect(Collector<? super T, A, R> collector);

<R> R collect(Supplier<R> supplier,
                  BiConsumer<R, ? super T> accumulator,
                  BiConsumer<R, R> combiner);
```

`collect` 是收集操作。通过流将其转换为其它常用的数据结构并收集，比如转换成 `List`、`Map` 等操作。

从上述源码中，看出 `collect` 方法一共有两个方法。

第一个方法从接收的参数可以看出，`collect` 方法是通过 `java.util.stream.Collector` 类来收集流元素到结果集中的。

```java
public class CollectOperator {
    public static void main(String[] args) {
        List<String> months1 = Stream.of("Jan", "Feb", "Mar", "Apr", "May",
                "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec", "Jun", "Jun", "Feb", "Jan")
                .filter(s -> "J".equals(s.substring(0, 1)))
                .collect(Collectors.toList());
        System.out.println(months1);
    }
}
```

输出结果：

```shell
[Jan, Jun, Jul, Jun, Jun, Jan]
```

从结果中看到，我们通过 `filter` 过滤掉首字母不是 `J` 的字符串，并通过 `collect` 转换为 `List` 收集起来，但是收集的集合中有重复的元素，因此，我们将其转换成 `Set` 在收集。

```java
public class CollectOperator {
    public static void main(String[] args) {
        Set<String> months = Stream.of("Jan", "Feb", "Mar", "Apr", "May",
                "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec", "Jun", "Jun", "Feb", "Jan")
                .filter(s -> "J".equals(s.substring(0, 1)))
                .collect(Collectors.toSet());
        System.out.println(months);
    }
}
```

输出结果：

```shell
[Jul, Jun, Jan]
```

输出的结果不是有序的，为了保证元素的有序，我们将元素存储在 `TreeSet` 中。`Collectors` 中没有 `toTreeSet()` ，因此我们通过 `Collectors.toCollection(Supplier<C> collectionFactory)` 来构建我们需要的集合类型。

```java
Stream.of("Jan", "Feb", "Mar", "Apr", "May",
                "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec", 
                "Jun", "Jun", "Feb", "Jan")
                .filter(s -> "J".equals(s.substring(0, 1)))
                .collect(Collectors.toCollection(TreeSet::new));
```

输出结果：

```shell
[Jan, Jul, Jun]
```

`collect` 也可以生成 `Map`。代码如下：

```java
class Pair {
    private final Character c;
    private final Integer i;
    Pair(Character c) { this.c = c;this.i = (int) c; }
    public Character getC() { return c; }
    public Integer getI() { return i; }
    @Override
    public String toString() { return "Pair{" + "c=" + c + ", i=" + i + '}'; }
}
public class CollectOperator {
    public static void main(String[] args) {
        Map<Character, Integer> charTables = Stream.of('c', 'a', 'z', 'o', 'y')
                .map(c -> new Pair(c)).collect(Collectors.toMap(Pair::getC, Pair::getI));
        System.out.println(charTables.toString());
    }
}
```

输出结果：

```shell
{a=97, c=99, y=121, z=122, o=111}
```

对于第二个方法，参数 `Supplier` 是一个生成目标类型实例的方法，代表着存储类型的对象；`BiConsumer<R, ? super T> accumulator` 参数是将操作目标数据填充到 `Supplier` 生成的目标类型的实例中去的方法，代表着如何将元素添加到容器中；而 `BiConsumer<R, R> combiner` 是将多个 `Supplier` 生成的实例整合到一起的方法，代表着规约操作，将多个结果合并。

下面给出一个简单的例子，统计月份出现的频次：

```java
public class CollectOperator {
    public static void main(String[] args) {
        Map<String, Integer> wordCount = Stream.of("Jan", "Feb", "Mar", "Apr", "May",
                "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec", "Jun", "Jun", "Feb", "Jan")
                .collect(TreeMap::new,
                        (map, word) ->
                                map.merge(word, 1, Integer::sum),
                        Map::putAll);
        System.out.println(wordCount);
    }
}
```

输出结果：

```shell
{Apr=1, Aug=1, Dec=1, Feb=2, Jan=2, Jul=1, Jun=3, Mar=1, May=1, Nov=1, Oct=1, Sep=1}
```

### reduce

`reduce` 的方法如下：

```java
T reduce(T identity, BinaryOperator<T> accumulator);
Optional<T> reduce(BinaryOperator<T> accumulator);
<U> U reduce(U identity,
                 BiFunction<U, ? super T, U> accumulator,
                 BinaryOperator<U> combiner);
```

`reduce` 是通过反复的对一个输入序列的元素进行某种组合操作（求和、最大值或将所有元素都放入一个列表），最终将其组合为一个单一的概要信息。

```java
public class ReduceOperator {
    public static void main(String[] args) {
         String concat = Stream.of("Jan", "Feb", "Mar", "Apr", "May",
                 "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec")
                 .filter(s -> "J".equals(s.substring(0, 1)))
                 .reduce("", (a, b) -> {
                     if (!"".equals(a)) {
                         return a + "," + b;
                     }
                     return b;
                 });
        System.out.println(concat);
    }
}
```

输出结果：

```shell
Jan,Jun,Jul
```

`Stream`中关于 `min`、`max`、`count` 的源码如下：

```java
Optional<T> min(Comparator<? super T> comparator);
Optional<T> max(Comparator<? super T> comparator);
long count();
```

`count` 的作用是求流中元素个数。

`min` 的作用是根据传入的 `Comparator` 来求 "最小" 的元素。

`max` 的作用是根据传入的 `Comparator` 来求 "最小" 的元素。

它们的底层都是依赖 `reduce` 实现的。

现在看一下例子代码：

```java
public class InfoReduceOperator {
    public static void main(String[] args) {
        String[] months = {"Jan", "Feb", "Mar", "Apr", "May",
                "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"};
        long count = Stream.of(months)
                .filter(s -> "J".equals(s.substring(0, 1)))
                .count();
        System.out.printf("计算首字母是`J`的字符串的个数是：%d个",count);
        System.out.println();
        String min = Stream.of(months).min(Comparator.comparing(Function.identity())).get();
        System.out.printf("字符串数组中最小的字符串是：%s", min);
        System.out.println();
        String max = Stream.of(months).max(String::compareTo).get();
        System.out.printf("字符串数组中最大的字符串是：%s", max);
    }
}
```

输出结果：

```shell
计算首字母是`J`的字符串的个数是：3个
字符串数组中最小的字符串是：Apr
字符串数组中最大的字符串是：Sep
```

### match

`match` 是匹配操作，它总共有三类方法：`anyMatch`、`allMatch` 和 `noneMatch` 。

`anyMatch` 用于检查所有元素中至少有一个是匹配的。它的方法如下：

```java
boolean anyMatch(Predicate<? super T> predicate);
```

`allMatch` 用于检查所有元素是否都匹配。方法如下：

```java
boolean allMatch(Predicate<? super T> predicate);
```

`noneMatch` 用于检查所有元素都没有匹配的。方法如下：

```java
boolean noneMatch(Predicate<? super T> predicate);
```

下面看一下关于上面方法的使用例子：

```java
public class MatchOperator {
    public static void main(String[] args) {
        String[] months = {"Jan", "Feb", "Mar", "Apr", "May",
                "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"};
        boolean isAnyMatch = Stream.of(months).anyMatch(s -> "A".equals(s.substring(0, 1)));
        System.out.printf("判断months数组中是否存在首字母为`A`：%s",isAnyMatch);
        System.out.println();
        boolean isAllMath = Stream.of(months).allMatch(s -> "A".equals(s.substring(0, 1)));
        System.out.printf("判断months数组中是否所有元素的首字母都为`A`：%s", isAllMath);
        System.out.println();
        boolean isNoneMath = Stream.of(months).noneMatch(s -> "A".equals(s.substring(0, 1)));
        System.out.printf("判断months数组中的首字母都不为`A`：%s", isNoneMath);
    }
}
```

输出结果：

```shell
判断months数组中是否存在首字母为`A`：true
判断months数组中是否所有元素的首字母都为`A`：false
判断months数组中的首字母都不为`A`：false
```

### find

`find` 是查找操作，它总共有三类方法：`findFirst` 和 `findAny`。

`findFirst` 的作用是返回当前流中的第一个元素。方法如下：

```java
Optional<T> findFirst();
```

`findAny` 的作用是返回当前流中的任意元素。方法如下：

```java
Optional<T> findAny();
```

我们来一下它们的使用方法：

```java
public class FindOperator {
    public static void main(String[] args) {
        String[] months = {"Jan", "Feb", "Mar", "Apr", "May",
                "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"};
        String first = Stream.of(months).parallel()
                .filter(s -> "J".equals(s.substring(0, 1)))
                .findFirst().get();
        System.out.printf("获取流中第一个元素：%s", first);
        System.out.println();
        String any = Stream.of(months).parallel()
                .filter(s -> "M".equals(s.substring(0, 1)))
                .findAny().get();
        System.out.printf("获取流中任意一个元素：%s", any);
    }
}
```

输出结果：

```shell
获取流中第一个元素：Jan
获取流中任意一个元素：Mar
```

`findFirst()` 无论流是否是并行化，总是选择流中的第一个元素。

对于非并行流，`findAny()` 会选择流中的第一个元素。但当我们使用 `parallel()` 来并行化后，`findAny()`才实现了选择任意元素。

如果必须选择流中最后一个元素，那就使用 `reduce()`。

```java
String last = Stream.of(months).parallel()
                .filter(s -> "J".equals(s.substring(0, 1)))
                .reduce((v1, v2) -> v2).get();
```

`reduce()` 的参数只是用最后一个元素替换了最后两个元素，最终只生成最后一个元素。

# 小结

使用 `Stream` 流对于Java是一个极大的提升，使用它编写代码看起来较为简洁优雅，并且`Stream`流的并行化极大的发挥了现在多核时代的优势。