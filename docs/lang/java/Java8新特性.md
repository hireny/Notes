Java 8新增了许多特性，我们主要讨论几个方面。

## 接口的默认方法与静态方法

Java 8 引入了 `default` 关键字实现默认方法，来将新功能添加到接口中，并确保与旧版本的接口编写的代码的二进制兼容性。

```java
public interface FunctionDefaultMethods {
	default void defaultMethod() {
	}
}
```

上述就是在接口中实现的默认方法，然而，实现多接口的类在继承默认方法且方法名称相同时，会造成方法冲突。

```java
interface Interface1 {
    default void run() {
        System.out.println("Interface1 method: run");
    }
}
interface Interface2 {
    default void run() {
        System.out.println("Interface1 method: run");
    }
}
public class InterfaceMethods implements Interface1, Interface2 {
    public static void main(String[] args) {
        InterfaceMethods methods = new InterfaceMethods();
        methods.run();
    }
}
```

`InterfaceMethods` 实现了 `Interface1` 与 `Interface2` 接口，两个接口有相同的默认方法，因此会在编译时报错：` inherits unrelated defaults for run() from types ...`。

而解决办法就是重写默认方法，并在方法中指定哪个接口的默认方法为实现版本。

```java
public class InterfaceMethods implements Interface1, Interface2 {
    public static void main(String[] args) {
        InterfaceMethods methods = new InterfaceMethods();
        methods.run();
    }

    @Override
    public void run() {
        Interface1.super.run();
    }
}
```

上述例子指定了默认方法的实现以 `Interface1` 接口为准。

Java 8还可以在接口中声明静态方法。

```java
public interface FunctionStaticMethods {
	static void run() {}
}
```
<br/>

## 函数式编程

Java 8 引入了 Lambda表达式，来实现函数式编程方式。

Lambda允许将函数作为一个方法的参数，或把代码本身当作数据处理。

Lambda表达式可由逗号分隔的参数列表、`->` 符号和语句块组成：

```java
Arrays.asList("Monday","Tuesday","Wednesday").forEach(e -> System.out.println(e));
```

现有功能为了与Lambda表达式进行兼容，推出了**函数接口**。它只有一个函数，可以隐式转换为Lambda表达式。但你要在接口中在添加一个函数，就会破坏它。因此，Java 8提供了 `@FunctionInterface` 接口，它用以标记接口为函数接口，当你在添加函数时，会编译报错。

```java
@FunctionInterface
public interface Function {
	void method();
	default void default1() {}
	static void static1() {}
}
```

而静态方法和默认方法不会破坏函数式接口的定义。

**方法引用**为已经具有名称的方法提供易于阅读的Lambda表达式。如下所示：

```java
String[] strings = {"Wednesday", "Monday", "Tuesday"};
Arrays.sort(strings, String::compareTo);
```

更详细的函数式编程讲解可查看[这篇文章](这篇文章)

## 对注解的扩展

注解添加了**重复注解**用以在同一个声明或类型的使用多次应用相同的注解类型。

在 Java 8 中添加了 `@Repeatable` 注解实现重复注解。

```java
@Repeatable(Authors.class)
@interface Author {
	String name();
}
@interface Authors {
	Author[] value();
}
@Author(name="a")
@Author(name="b")
class Article {}
```

如果我们想在 Java 8 之前实现相同相同的功能，需要实现一个注解容器 `@Authors`。

```java
@interface Author {
	String name();
}
@interface Authors {
	Author[] value();
}
@Authors({@Author(name="a"), @Author(name="b")})
class Article {}
```

注解还为 `ElementType` 添加了新枚举元素 `TYPE_PARAMETER`、`TYPE_USER`，从而可以使用 `@Target{ElementType.TYPE_USER}` 修饰注解定义，这种注解被称为**类型注解**，可以用在任何使用到类型的地方。

`TYPE_PARAMETER`：表示该注解能写在类型参数的声明语句中。

```java
@Target(ElementType.TYPE_PARAMETER)
public @interface TypeParameter {
}
public class TyeParameterClient<@TypeParameter String> {
}
```

`TYPE_USER`：表示该注解可以写在使用类型的任何语句中（eg：声明语句、泛型和类型转换）。

```java
@Target(ElementType.TYPE_USE)
@interface NotNull{
}
// 在
@NotNull
public interface NotnullInterface {
}
// 在 implements 语句中使用
public class NotNullClient implements @NotNull Serializable {
	// 在 throws 语句中使用
    public static void main(String[] args) throws @NotNull Exception {
        // 创建实例时使用
        TypeParameterClient<String> typeParameter = new @NotNull TypeParameterClient<>();
        
        // 类型转换时使用
        Object s = "String";
        String fs = (@NotNull String) s;
        
    }
    
    // 方法形参上使用
    public String methodParamter(@NotNull String s1, String s2) {
        return s1 + s2;
    }

    // 泛型中使用
    public String methodParamter(List<@NotNull String> list) {
        return null;
    }
}
```

这种无处不在的注解，可以让编译器执行更严格的代码检查，从而提高程序的健壮性。

需要指出的是，上面程序虽然使用了大量 `@NotNull` 注解，但是这些注解暂时不会起任何作用——因为没有为这些注解提供处理工具。

Java 8 本身并没有提供，要想要这些注解发挥作用，需要开发者自己实现，或者使用第三方提供的工具。

## 类型推断 

Java 8 对于类型推断做了改进。
比如在 Java 7 中下面的写法：

```java
List<String> stringList = new ArrayList<>();
stringList.add("A");
stringList.addAll(Arrays.<String>asList());
```

在 Java 8 中改进后的写法，可以自动做类型推断。
```java
List<String> stringList = new ArrayList<>();
stringList.add("A");
stringList.addAll(Arrays.asList());
```

下面是新增的API。

## Optional

Java 8 新增了 `Optional` 类用来解决 `NPE`（空指针异常）。

`Optional` 可以看成是一个保存值的容器，它可以保存泛型类型的值，或者仅保存 `null` 值。

我们来看一个使用`Optional`的小例子：

```java
Optional<String> name = Optional.ofNullable(null);
name.isPresent();	// false (判断Optional容器中是否存在值)
name.orElseGet(()->"[none]");	// [none] (用于获取name容器中的值，如果无值则返回orElseGet中[none]字符串)
```

关于 `Optional` 的详细信息可以查看[这篇文章](这篇文章)。

## Stream API

Java 8 新增了 `Stream API` 来支持对元素流进行函数式操作。`Stream API` 除了有 `Stream` 等一些专门支持对流的处理的类，还集成在 `Collections API` 中，可以对集合进行批量操作，例如顺序或并行的 `map-reduce` 转换。

```java
// 月份对照
enum MonthOfYear {
    JAN("Jan", "一月"),FEB("Feb", "二月"),MAR("Mar", "三月"),APR("Apr", "四月"),
    MAY("May", "五月"),JUN("Jun", "六月"),Jul("Jul", "七月"),AUG("Aug", "八月"),
    SEP("Sep", "九月"),OCT("Oct", "十月"),NOV("Nov", "十一月"),DEC("Dec", "十二月");
    private String value;
    private String chinese;
    MonthOfYear(String value, String chinese) {
        this.value = value;
        this.chinese = chinese;
    }
    ...省略getter...
    public static MonthOfYear monthOfYear(String value) {
        for (MonthOfYear month : MonthOfYear.values()) {
            if (month.value == value) {
                return month;
            }
        }
        return null;
    }
}
String[] months = {"Jan", "Feb", "Mar", "Apr", "May",
                "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec", "Jun", "Jun", "Feb", "Jan"};

// 数组转换为List集合
Arrays.asList(months)
	.stream()
	.filter(s -> "J".equals(s.substring(0, 1)))
	.map(MonthOfYear::monthOfYear)
	.filter(Objects::nonNull)
	.map(s -> s.getChinese() + " ")
	.sorted()
	.forEach(System.out::print);
/**
 * 输出结果：
 * 一月 一月 七月 六月 六月 六月 
 */
```

关于`Stream` 的一些浅见可以查看[这篇文章](这篇文章)。

## Date/Time API

Java 8 通过新的 `Date0Time API(JSR 310)` 来加强对日期与时间的处理。其中包括 `LocalDate`、`LocalTime`、`LocalDateTime`等。新的日期不允许有任何改变，要修改时间的，会返回类的一个新实例。

```java
LocalDate now = LocalDate.now();
System.out.println(now);
System.out.println(now.getYear());
System.out.println(now.getMonth());
System.out.println(now.getDayOfMonth());

LocalTime localTime = LocalTime.now();
System.out.println(localTime);
LocalDateTime localDateTime = now.atTime(localTime);
System.out.println(localDateTime);
```

关于 `Date-Time API` ，可以查看[这篇文章](这篇文章)

## Base64

Base64编码已经在Java 8 时称为类库的标准。它使用非常简单：

```java
public static void main(String[] args) {
	final String text = "The is a test of Base64 encoding";
	final String encoded = Base64.getEncoder()
		.encodeToString(text.getBytes(StandardCharsets.UTF_8));
	System.out.println(encoded);
	
	final String decoded = new String(Base64.getDecoder().decode(encoded), StandardCharsets.UTF_8);
	System.out.println(decoded);
}
/**
 * 输出结果：
 * VGhlIGlzIGEgdGVzdCBvZiBCYXNlNjQgZW5jb2Rpbmc=
 * The is a test of Base64 encoding
 */
```

`Base64` 还提供了一些其它的支持，详情可查看源码或官方文档。

## 其它特性

- Java 8提供了并行数组排序 `Arrays.parallelSort`。
- 无符号算术支持 `Long.parseUnsignedLong`。
- 提供新的 Nashorn JavaScript引擎来替代 Rhino JavaScript引擎。

Java 8还有许多其它新的特性，我们就不一一介绍了，详情可查看[官方文档](https://www.oracle.com/cn/java/technologies/javase/8-whats-new.html)

- https://www.oracle.com/cn/java/technologies/javase/8-whats-new.html