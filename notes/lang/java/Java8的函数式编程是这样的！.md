# 介绍

Java语言是面向对象思想的编程语言，它跟函数式编程没有关系，至从Java 8开始添加的Lambda表达式等新特性开始，Java也开始支持函数式编程。

函数式编程中，函数是第一等公民。

下面我们来看一下Java 8 前后调用 `Runnable` 线程的对比：

```java
class Printer {
    static void print() {
        System.out.println("Printer::print()");
    }
}
public class RunnableLambdaExpressions {
    public static void main(String[] args) {
        // Java 8 之前使用匿名内部类
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("Anonymous");
            }
        }).start();
        // Java 8 之后使用 Lambda 表达式来缩短代码
        new Thread(
                () -> System.out.println("lambda")
        ).start();
        // Java 8 之后也可以使用方法引用来替代匿名内部类
        new Thread(Printer::print).start();
    }
}
```

输出结果：

```shell
Anonymous
lambda
Printer::print()
```

# Lambda

Lambda 表达式是 Java 8 的新特性，用于实现函数式编程，底层还是生成匿名类来实现的。Lambda 表达式本质上是一个匿名方法，但这个方法不是独立执行的，而是用于实现由函数式接口定义的另一个方法。因此，Lambda表达式会导致生成一个匿名类。

Lambda 表达式的基本语法是：`(parameters) -> expression` 或者 `(parameters) -> {statements;}`。

1. `parameters`：类似方法中的形参列表，这里的参数是函数式接口里的参数。这里的参数类型可以显示指定也可以不声明，因为很多时候参数的类型是可以推断出来的，当只有一个推断类型时可以省略掉圆括号。
2. 接着 `->` ，可以理解为 "被用于" 的意思 。
3. `->` 之后的内容都是方法体，可以是表达式也可以是代码块，实现函数式接口中的方法。这个方法体可以有返回值也可以没有返回值。

Lambda表达式单行时，自动生成返回值，使用 return 是非法的；多行时，则必须使用 {} 括起来，并且使用 return。

```java
interface Console {
    void log(String msg);
}

interface Multi {
    String threeArgs(String desc, Double d1, Double d2);
}

public class LambdaExpressions {
    public static void main(String[] args) {
        Console console = (h) -> System.out.println("Log Info [" + h + "]");
        console.log("测试带参数无返回值的lambda表达式");

        Multi multi = (desc, n1, n2) -> {
            Double sum = n1 + n2;
            return desc + "[" + n1 + "+" + n2 + "=" + sum + "]";
        };
        String value = multi.threeArgs("计算两数之和：", 3.147, 2.15);
        System.out.println(value);
    }
}
```

输出结果：

```shell
Log Info [测试带参数无返回值的lambda表达式]
计算两数之和：[3.147+2.15=5.297]
```

### 闭包

Lambda表达式可以访问静态变量、类的实例变量以及被 `final` 修饰的局部变量。访问静态变量与实例变量与其它方式并无区别，只有在访问局部变量时，必须是被 `final` 修饰过后的，没有被 `final` 修饰的局部变量，当Lambda表达式调用该局部变量时也会被隐式地声明为被`final`修饰的常量。

```java
public class LambdaScopes {
    public static void main(String[] args) {
        int num = 1;    // 等价于 `final int num = 1;`
        Function<Integer, String> f2 = from -> {
            // 可以访问外部局部变量，但不能修改 num 
            int i = from + num;
            // num++;  这里会报错 `Variable used in lambda expression should be final or effectively final`
            // 意思是在lambda表达式中使用变量应该是 `final` 或 有效的 `final`
            return String.valueOf(i);
        };
        f2.apply(num);
    }
}
```

# 函数式接口

我们都知道 Lambda 表达式要调用接口才能使用，但不是所有的接口都能使用Lambda表达式呢。Lambda表达式要调用的接口必须只有一个抽象方法，这就是函数式接口。

但是函数式接口中可以有多个非抽象方法，即 Java 8 的新特性：被 `default` 修饰的默认方法。

Java 8 新添加了一个注解 `@FunctionalInterface` ：被该注解修饰的接口强制执行 "函数式方法" 模式，即只有一个抽象方法。

```java
@FunctionalInterface
interface Converter<F,T> {
    T convert(F from);
}
public class FunctionalAnnotations {
    public static void main(String[] args) {
        // 将数字字符串转换为整数类型
        Converter<String, Integer> converter = Integer::valueOf;
        Integer converted = converter.convert("123456");
        System.out.println(converted.getClass());
    }
}
```

`@FunctionalInterface` 注解是可选的；例如JDK中的 `Runnable`接口也是函数式接口。

```java
public interface Runnable {
	void run();
}
```

当接口中存在多个方法时，使用 `@FunctionalInterface` 注解会使接口产生编译时错误。

![函数式接口多方法错误](./images/Java 函数式接口多方法错误.png)

上述图片会报编译时错误信息：`Multiple non-overriding abstract methods found in interface xx.xx.Converter`。

在 Java 中任何对象的实现默认都继承了 `Object` 类，函数式接口也不意外，因此在函数式接口中定义的 `java.lang.Object` 中的 `public` 方法，默认都包含了 `Object` 类中这些抽象方法的实现。

完全符合函数式接口的定义：

```java
@FunctionalInterface
interface Converter<F,T> {
    T convert(F from);
    // 默认方法
    default void defaultMethod() {
    	System.out.println("default method");
    }
    // 静态方法
    static void staticMethod() {
    	System.out.println("static method");
    }
    // Object方法
    @Override
    boolean equals(Object o);
}
```

### 常用函数式接口

#### Predicate

`Predicate` 接口中的抽象方法是用来进行条件判断的，接口如下：

```java
@FunctionalInterface
public interface Predicate<T> {
    boolean test(T t);
}
```

我们对 `Predicate` 实现一个例子，来说明 `test()` 方法的作用，判断字符串中是否存在字符d：

```java
Predicate<String> p1 = s -> s.contains("d");
boolean b1 = p1.test("Hello World!");
```

`Predicate` 中有三个默认方法，是条件判断中的与、或、非三种逻辑关系：

```java
// 逻辑关系中的与
default Predicate<T> and(Predicate<? super T> other) {
	Objects.requireNonNull(other);
	return (t) -> test(t) && other.test(t);
}
// 逻辑关系中的非
default Predicate<T> negate() {
	return (t) -> !test(t);
}
// 逻辑关系中的或
default Predicate<T> or(Predicate<? super T> other) {
	Objects.requireNonNull(other);
	return (t) -> test(t) || other.test(t);
}
```

对上述三个默认方法的使用：

```java
Predicate<String> p1 = s -> s.contains("d");
Predicate<String> p2 = s -> s.contains("h");
// 该表达式是 判断 "Hello World!" 是否同时存在 d 和 h
boolean b2 = p1.and(p2).test("Hello World!");

Predicate<String> p3 = s -> s.contains("f");
// 该表达式是 判断 "Hello World!" 是否存在 d 或 f
boolean b3 = p1.or(p3).test("Hello World!");

Predicate<String> p4 = s -> s.contains("o");
// 该表达式是 判断 "Hello World!" 不存在 o
boolean b4 = p4.negate().test("Hello World!");
```

`Predicate` 中还有两个静态方法：

```java
// 判断两个对象是否相等
static <T> Predicate<T> isEqual(Object targetRef) {
	return (null == targetRef)
		? Objects::isNull
		: object -> targetRef.equals(object);
}
// 对 表达式 取反
@SuppressWarnings("unchecked")
static <T> Predicate<T> not(Predicate<? super T> target) {
	Objects.requireNonNull(target);
	return (Predicate<T>)target.negate();
}
```

#### Function

`java.util.function.Function<T,R>` 接口如下：

```java
@FunctionalInterface
public interface Function<T, R> {
	// 根据泛型类型 T 的参数获取类型 R 的结果
    R apply(T t);
}
```

如下例子所示，将一个数字类型转换为字符串类型：

```java
Function<Number, String> function = String::valueOf;
String apply = function.apply(12);
```

`Function` 接口中有两个默认方法，是用来进行组合操作。

`compose()` 方法源码如下：

```java
default <V> Function<V, R> compose(Function<? super V, ? extends T> before) {
	Objects.requireNonNull(before);
	// 先执行 before 的 apply 方法，后执行调用者
	return (V v) -> apply(before.apply(v));
}
```

`andThen()` 源码如下：

```java
// 这里的V 一个作为输入值，一个作为输出值 按照调用的顺序不同，对于 T V 做输入，输出的顺序也不同
default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) {
	Objects.requireNonNull(after);
	// 先执行调用者，再执行 after 的 apply 方法
	return (T t) -> after.apply(apply(t));
}
```

还有一个静态方法 `identity()` 方法，源码如下：

```java
// 返回一个执行了 apply() 方法之后只会返回输入参数的函数对象
static <T> Function<T, T> identity() {
	return t -> t;
}
```

调用自身，输入对象就是输出对象。

下面根据 `Function` 函数式接口中的方法实现如下例子：

```java
public class FunctionTest {
    public static void main(String[] args) {
        String str = "赵钱孙李,100";
        // 截取字符串
        Function<String, String> f1 = s -> s.split("\\,")[1];
        // 将数字字符串转换为数字
        Function<String, Integer> f2 = Integer::parseInt;
        // 数字累加100，得到新的数字
        Function<Integer, Integer> f3 = s -> s+= 100;

		// 该例子先调用f1表达式，再调用f2表达式，最后在调用f3表达式
        Integer apply = f3.compose(f1.andThen(f2)).apply(str);
        System.out.println(apply);
    }
}
/*
输出结果：
200
*/
```

#### Consumer

`java.util.function.Consumer<T>` 接口用于接收一个泛型对象，无返回值，源码如下：

```java
@FunctionalInterface
public interface Consumer<T> {
	// 接收一个泛型T类型的对象
    void accept(T t);
}
```

它还有一个默认方法：

```java
default Consumer<T> andThen(Consumer<? super T> after) {
	Objects.requireNonNull(after);
	return (T t) -> { accept(t); after.accept(t); };
}
```

该默认方法的作用是使两个 `Consumer` 实现组合操作。

```java
public class ConsumerTest {
    public static void main(String[] args) {
        // 该 lambda 作用是对字符串以 `,` 进行分割并打印第一个元素
        Consumer<String[]> c1 = v1 -> {
            for (int i = 0; i < v1.length; i++) {
                System.out.printf(v1[i].split("\\,")[0] + " ");
            }
        };
        // 该 lambda 作用是对字符串以 `,` 进行分割并打印第二个元素
        Consumer<String[]> c2 = v2 -> {
            System.out.println();
            for (int i = 0; i < v2.length; i++) {
                System.out.printf(v2[i].split("\\,")[1] + " ");
            }
        };

        // c1 调用
        c1.accept(new String[]{"小赵,男", "小钱,未知", "小孙,女"});
        System.out.println();

        // c1 与 c2 使用默认方法进行组合
        c1.andThen(c2).accept(new String[]{"小赵,男", "小钱,未知", "小孙,女"});
        System.out.println();

        // 自身消费
        String[] datas = new String[]{"小李,女", "小周,女", "小王,男"};
        Consumer<String> one = s -> System.out.print(s.split("\\,")[0]);
        Consumer<String> two = s -> System.out.println("," + s.split("\\,")[1]);
        for (String info : datas) {
            one.andThen(two).accept(info);
        }
    }
}
```

#### Supplier

`java.util.function.Supplier<T>` 接口用于获取一个泛型参数指定类型的对象数据：

```java
@FunctionalInterface
public interface Supplier<T> {
    T get();
}
```

由于是一个函数式接口，这意味着对应的 Lambda 表达式需要 "对外提供" 一个服务泛型类型的对象数据。

```java
public class SupplierTest {
    public static void main(String[] args) {
        // 定义一个 Integer 类型的数组并赋值
        Integer[] data = new Integer[]{31, 23, 66, 12, 9};
        // 定义一个 Supplier 获取最大值的提供者
        Supplier<Integer> supplier = () -> {
            // 获取数组最大值并返回
            int max = data[0];
            for (Integer datum : data) {
                max = Math.max(max, datum);
            }
            return max;
        };
        int result = supplier.get();
        System.out.println(result);
    }
}
```

扩展函数比较多，但是掌握了上面几个基本的接口，其它接口使用方式基本相同。

`java.util.function` 包旨在创建一组完整的目标接口，使得我们一般情况下不需要再定义自己的接口。这主要是因为基本类型会产生一小部分接口。如果你了解命名模式，顾名思义就能知道特定接口的作用。

### 高阶函数

函数式编程中有一个很大的特点就是高阶函数。

"函数式" 语言中， "函数" 都是 "第一公民"，也就是说，函数可以像整数，浮点数，子串等一样，作为函数的参数，成员变量与返回值。

Java中的函数不是第一公民，函数只是一个虚成员函数的接口。

高阶函数是指参数是函数，或返回值是函数。

下面是一个高阶函数的例子，利用局部变量域特性，进行延迟求值：

```java
public class HigherOrderFunctionTest {
    /**
     * 输入一定数量的参数，然后统一求值
     * @param size      需要求值的个数
     * @param fn        求值函数
     * @return          函数对象
     *
     * 从函数的定义就可以看出，Java函数编程的内在思想还是面向对象
     */
    public IntFunction<Integer> result(int size, ToIntFunction<List<Integer>> fn) {
        // 声明局部变量，用于存储传入参数
        final List<Integer> args = new ArrayList<>();
        return value -> {
            // 没有达到定义的数量之前，不求值
            int result = 1;
            if (args.size() == size) {
                result = fn.applyAsInt(args);
            } else {
                args.add(value);
            }
            // 返回结果
            return result;
        };
    }

    public static void main(String[] args) {
        // 准备测试对象
        IntFunction<Integer> fun = new HigherOrderFunctionTest().result(3, items -> {
            // 利用 reduce 进行求值
            return items.stream().reduce(0, Integer::max);
        });
        // 方法调用还很生硬，有个莫名其妙的函数名 apply，可能会引起业务的误解
        fun.apply(9);
        fun.apply(29);
        fun.apply(21);
        // 超过了数量不求值
        int result = fun.apply(4);
        System.out.println(result);
    }
}
```

# 方法引用

Lambda表达式还提供一种更简单的方法调用，就是方法引用。

方法引用组成：类名或对象名，后面跟 `::`，然后跟方法名称。

```java
public class MethodReferences {
    static class Description {
        String about;
        Description(String desc) {
            about = desc;
        }
        void help(String msg) {
            System.out.println(about + " " + msg);
        }
    }
    public static void main(String[] args) {
    	Consumer<String> c = i -> {
            Description a = new Description("Valuable");
            a.help(i);
        };
        c.accept("information");
    }
}
```

在这里我们用方法引用代替 Lambda 表达式：

```java
Consumer<String> c = new Description("Valuable")::help;
c.accept("information");
```

这里的 `new Description("Valuable")::help` 可以看作为 Lambda 表达式的简写形式。尽管方法引用不一定会把语法变得更紧凑，但它拥有更明确的语义 —— 如果我们想要调用的方法拥有一个名字，我们就可以通过它的名字直接调用它。

### 分类

方法引用可分为以下几类：

- 静态方法的方法引用；
- 实例方法的方法引用；
- 实例对象的方法引用；
- 构造方法的方法引用。

#### 静态方法的方法引用

```java
class Logger {
    // 静态方法
    static void info(String msg) {
        System.out.println(msg);
    }
}
```

通过 Lambda 表达式，调用其静态方法：

```java
Consumer<String> c = s -> Logger.info(s);
c.accept("static method reference")
```

上述的静态方法调用可以使用方法引用：

```java
Consumer<String> c = Logger::info;
c.accept("static method reference");
```

#### 实例方法的方法引用

```java
class Length {
    private Integer size;
    public Length(Integer size) { this.size = size; }
    int compore(Length o) { return this.size.compareTo(o.size); }
	@Override
    public String toString() {
        return "Length{" + "size=" + size + '}'; 
    }
}
```

通过 Lambda 表达式调用其实例方法：

```java
Arrays.sort(lengths, (l1, l2) -> l1.compore(l2));
```

上述的实例方法调用可以改为实例方法的方法引用：

```java
Arrays.sort(lengths, Length::compore);
```

#### 实例对象的方法引用

```java
class Describe {
    void show(String msg) {
        System.out.println(msg);
    }
}
```

通过 Lambda 表达式调用其实例方法：

```java
Describe d = new Describe();
Consumer<String> c = (a) -> d.show(a);
c.accept("instance method reference");
```

上述的表达式可以改为实例对象的方法引用：

```java
Consumer<String> c = new Describe()::show;
c.accept("instance object method reference");
```


#### 构造方法的方法引用

你还可以捕获构造函数的引用，然后通过引用调用该构造函数。

```java
class Student {
    String name;
    int age = -1;    // For "unknown"
    Student() { name = "Jan"; }
    Student(String name) { this.name = name; }
    Student(String name, int age ) { this.name = name;this.age = age; }

    @Override
    public String toString() {
        return "Student{" + "name='" + name + '\'' + ", age=" + age + '}';
    }
}

interface MakeNoArgs {
    Student make();
}

interface Make1Arg {
    Student make(String nm);
}

interface Make2Args {
    Student make(String nm, int age);
}
public class ConstructorReferences {
    public static void main(String[] args) {
        MakeNoArgs mna = Student::new;   // 方法引用的是无参构造
        Make1Arg m1a = Student::new;     // 方法引用的是1个参数的构造
        Make2Args m2a = Student::new;    // 方法引用的是2个参数的构造

        Student sn = mna.make();
        Student s1 = m1a.make("Feb");
        Student s2 = m2a.make("Mar", 24);

        System.out.println(sn);
        System.out.println(s1);
        System.out.println(s2);
    }
}
```

输出结果：

```shell
Person[name='Jan', age=-1]
Person[name='Feb', age=-1]
Person[name='Mar', age=24]
```

`Student` 有三个构造函数，函数接口内的 `make()` 方法反映了构造函数参数列表。

上面用的都是 `Student::new`，因为构造函数只有 `::new` 方法引用。编译器可以检测并将构造函数赋值给对应的接口进行调用。

# 总结

Java 8 新加入的 Lambda 表达式比较符合现今编程语言向函数式方向的靠拢，也让代码变得简洁，但是不易于后期的维护，可读性差。