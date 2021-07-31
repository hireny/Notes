在 Java 没有泛型之前，只能使用 `Object` 变量并在需要的地方使用强制类型转换，但这样会使得代码没有安全性和可读性。因此，Java 5 开始支持泛型。使用泛型编写的代码可以被很多不同类型的对象所重用。

## 定义泛型

泛型的本质是指参数化类型。所谓参数化类型，是指用来声明数据的类型本身，也是可以改变的，它由实际参数来决定。

泛型：把类型明确的工作推迟到创建对象或调用方法的时候才去明确的特殊的类型。

### 泛型类

下面定义一个简单的泛型。

```java
public class Holder<T> {
    private T a;
    public GenericHolder() {}
    public void set(T a) { this.a = a; }
    public T get() { return a; }

    public static void main(String[] args) {
        Holder<Integer> v = new Holder<Integer>();
        v.set(1024); // 此处有类型校验
        Integer a = v.get();  // 无需类型转换
    }
}
```

上面创建 `Holder` 实例时，要明确持有对象的类型，然后，`Holder` 实例就只能存储该类型或其子类的值。当调用 `get()` 取值获取的是该类型的值。

类定义中的类型变量是指方法的返回类型以及域和局部变量的类型。

`Holder<Integer> v = new Holder<Integer>()` 是 Java  5 中的写法，Java  7 中写法为 `Holder<Integer> v = new Holder<>()`，称为 “钻石语法”。

编写泛型时，需要定义泛型类型`<T>`；静态方法不能引用泛型类型`<T>`，必须定义其它类型（例如`<K>`） 来实现静态泛型方法；泛型可以同时定义多种类型，例如：`Map<K,V>`。

注意：类型变量使用大写形式，且比较短，这是很常见的。在 Java 库中，使用变量 E 表示集合的元素类型，K 和 V 分别表示表的关键字与值的类型。T（需要时还可以使用临近的字母 U 和 S）表示 “任意类型”。

### 泛型方法

下面定义一个带有类型参数的泛型方法。泛型处在方法的修饰符及其返回值之间。

```java
public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
    Set<E> result = new HashSet<>(s1);
    result.addAll(s2);
    return result;
}
```

上面的静态方法独立于类而改变方法，这样方式要比将类泛型化更清晰易懂。

对于泛型类，必须在实例化该类时指定类型参数。使用泛型方法时，通常不需要指定参数类型，因为编译器会找出这些类型。这称为 **类型参数推断**。

假如要在参数中使用可变参数时，会创建一个数组用来存放；这个数组是一个实现细节，是可见的。因此，当可变参数有泛型或者参数化类型时，编译警告信息就会产生混乱。

在之前，使用 `SuppressWarnings("uncheck")` 注解来消除警告。从 Java 7 开始，增加了 `@SafeVarargs` 注解，用来承诺声明的方法是类型安全的，并消除警告。

```java
 @SafeVarargs
public static <T> List<T> makeList(T... args) {
    List<T> result = new ArrayList<>();
    for (T item : args) {
        result.add(item);
    }
    return result;
}
```

## 类型限定

当对一个数组进行排序时，可以实现一个排序方法：

```java
public class Sort implements Serializable {
    public void sort(T[] source) {

        for (int i = 1; i < source.length; i++) {
            for (int j = 0; j < source.length - 1; j++) {
                if (source[j+1].compareTo(source[j]) < 0) {
                    T t = source[j];
                    source[j] = source[j+1];
                    source[j+1] = t;
                }
            }
        }
    }
}
```

但这里的 `source` 是使用泛型 `T[]` 表示的类型数组，当没有限定类型而执行类型擦除后，泛型数组 `T[]` 会使用 `Object[]` 引用，而 `Object` 类也没有实现 `Comparable` 接口，调用不了 `compareTo` 方法。因此，类或方法需要对类型变量施加约束，将 `T` 限制为实现了 `Comparable` 接口的类。如下所示：

```java
public class Sort<T extends Comparable<T> & Serializable> implements Serializable {
    public void sort(T[] source) {
        for (int i = 1; i < source.length; i++) {
            for (int j = 0; j < source.length - 1; j++) {
                if (source[j+1].compareTo(source[j]) < 0) {
                    T t = source[j];
                    source[j] = source[j+1];
                    source[j+1] = t;
                }
            }
        }
    }
}
```

而 `<T extends BoundingType>` 是一种类型限定的方式。

其中，`BoundingType` 为父类，而 `T` 表示为 `BoundingType` 类型的子类型。它的界限为 `[T, BoundingType]` 且 `T` 和 `BoundingType` 都在边界之内。一个类型变量可以有多个限定，且限定之间用 `&` 分隔。

```java
<T extends Comparable & Serializable>
```

## 通配符类型

泛型类型用起来较为方便，但参数化类型是不变的，当在 `Holder<T>` 类中增加一个比较方法。

```java
public boolean toEquals(Holder<T> obj) {
    return this.get() == obj.get();
}
```

当这种方式创建的对象进行比较时只能比较相同的类型。

```java
Holder<Integer> a = new Holder<>();
a.set(100);
Holder<Integer> b = new Holder<>();
b.set(200);
a.toEquals(b);  // 正确
Holder<Double> c = new Holder<>();
c.set(123.32);
a.toEquals(c);  // 错误
```

Java 提供了一种特殊的参数化类型，称作 **有限制的通配符类型**，它可以处理类似的情况。如下所示：

```java
public boolean toEquals(Holder<? extends Number> obj) {
    return this.get() == obj.get();
}
```

该方法的参数化类型为 `Number` 的子类。

```java
a.toEquals(c);  // 正确
```

因此，上面的方法就会正确无误地编译。

还可以指定一个超类型限定通配符，如下所示：

```java
? super T
```

这种方式表示的是 T 或 T 的一个父类型。因此上面的方法换一种写法：

```java
public boolean toEquals(Holder<? super Integer> obj) {
    return this.get() == obj.get();
}
```

该方法的参数化类型为 `Integer` 的父类。即为 `Integer` 和 `Number` 都可以，当与 `Double` 值比较时会报错。

```java
Holder<Integer> a = new Holder<>();
a.set(100);
Holder<Number> b = new Holder<>();
b.set(2003.02);
a.toEquals(b);  // 正确
Holder<Double> c = new Holder<>();
c.set(123.23);
a.toEquals(c);  // 报错
```

还可以使用无限定的通配符，如 `Holder<?>`，它与 `Holder<? extends Object>` 是一样的。

因此，要想获得最大限度的灵活性，要在参数类型上使用通配符类型。

下面的助记符便于让你记住要使用哪种通配符类型：

**PECS 表示 producer-extends，consumer-super**：如果参数化类型表示一个生产者 T，就使用 `<? extends T>`；如果它表示一个消费者 T，就使用 `<? super T>`。

## 泛型擦除

虚拟机没有泛型类型对象，所有对象都属于普通类。因此，定义的泛型最后都会擦除，并替换为限定类型，无限定类型的变量用 `Object` 替换。


```java
public class Holder<T> {
    private T a;
    public Holder() {}
    public void set(T a) { this.a = a; }
    public T get() { return a; }
}
```

T 是一个无限定的变量，所以直接用 `Object` 替换。

如果给定限定，原始类型用第一个限定的类型变量来替换。

```java
public class Holder<T extends Comparable & Serializable> implements Serializable {
    private T a;
    public Holder() {}
    public void set(T a) { this.a = a; }
    public T get() { return a; }
}
```

在执行类型擦除时，原始类型 `Holder` 如下所示：

```java
public class Holder implements Serializable {
    private Comparable a;
    public Holder() {}
    public void set(Comparable a) { this.a = a; }
    public Comparable get() { return a; }
}
```

这也说明，Java 提供的如 `List` 等集合类，不管使用时设置的是 `List<String>` 还是 `List<Integer>` ，在运行时都是相同的类型，都会擦除成原生类型 `List`。

## 模糊性错误

泛型的引入，增加了模糊性错误的可能。如泛型类 `Holder<U, S>` 中的两个泛型声明，在擦除后变成相同的类型而导致的冲突，就会发生模糊性错误。

```java
public class Holder<U, S> {
    U var1;
    S var2;

    //重载错误
    public void set(U u) {
        var1 = u;
    }
	//重载错误
    public void set(S s) {
        var2 = s;
    }
}
```

当声明两个相同的类型时，会导致 `set()` 方法的两个版本完全相同。而且，对 `set()` 方法的类型擦除会使两个版本都变为 `set(Object o)`，也会导致错误。

## 限制

### 基本类型

不能用类型参数代替基本类型。如 `Holder<T>` 中的泛型 `T` 只能是 `Object` 类及其子类，不能是 Java 中的基本类型。

### 类型查询

不能使用 `instanceof` 查询一个对象是否属于某个泛型类型。

```java
if (a instanceof Holder<String>)  // Error
```

而且， `getClass()` 方法返回的也是原始类型。

```java
Holder<String> s = new Holder<>();
s.getClass();  // class xx.xx.Holder
Holder<Integer> i = new Holder<>();
i.getClass();  // class xx.xx.Holder
```

### 类型变量

不能使用泛型来实例化变量。如使用  `new T(...)`，`new T[...]` 或 `T.class` 都是错误的。泛型只是一个占位符。

### 泛型类的静态上下文中类型变量无效

静态成员不能使用在类中声明的类型参数，但是可以声明静态的泛型方法

```java
public class Holder<T> {
    //错误
    private static T t;
    //错误
    public static T getT() {
        return t;
    }
    //正确
    public static <K> void test(K k) {
    }
}
```

### 泛型数组

不能实例化参数化类型的数组。如下所示：

```java
 Holder<String>[] holders = new Holder<>[10];
```

上面的方式是不允许的，但是声明类型为 `Holder<String>[]` 的变量是合法的。

### 泛型类型的实例

泛型类型的实例既不能被抛出也不能捕获。实际上，甚至泛型类扩展 `Throwable` 都是不合法的。

```java
// Error:Generic class may not extend 'java.lang.Throwable'
public CustomException<E> extends Exception {}
```

`catch` 子句中不能使用类型变量。例如，如下方法将不能编译：

```java
public static <T extends Throwable> void doWork(Class<T> t) {
    try {
        // do work
    } catch (T e) {
        // Error--can't catch type variable
    }
}
```

不过，在异常规范中使用类型变量是允许的。以下方法是合法的：

```java
public static <T extends Throwable> void doWork(T t) {
    try {
        // do work
    } catch (Throwable e) {
        t.initCause(e);
        throw t;
    }
}
```

欢迎关注公众号「海人为记」，期待与你共同进步！