# 泛型

- 一、泛型定义
- 二、编写泛型
- 三、泛型擦拭
- 四、边界
- 五、通配符
- 六、泛型限制


Java语言在没有泛型时，只能通过 `Object` 代表任意类型并对类型进行强制转换，这样的程序并不安全。

我们来看一下没有泛型的集合类的使用情况：

- `Collection`、`Map` 集合对元素的类型是没有任何限制的。本来我的Collection集合存储的是全部的Dog对象，但是外边把Cat对象存储到集合中，是没有任何语法错误的。
- 把对象扔进集合中，集合是不知道元素的类型是什么的，仅仅知道是Object。因此在get() 的时候，返回的是 Object。外边获取该独享，还需要强制转换

有了泛型以后：

- 代码更加简洁【不用强制转换】
- 程序更加健壮【只要编译时期没有警告，那么运行时期就不会出现ClassCastException异常】
- 可读性和稳定性【在编写集合的时候，就限定了类型】


## 一、泛型定义

Java泛型是 Java 5 新添加的语言特性之一，它的本质是指参数化类型。所谓参数化类型，是指用来声明数据的类型本身，也是可以改变的，它由实际参数来决定。

泛型：把类型明确的工作推迟到创建对象或调用方法的时候才去明确的特殊的类型。


## 二、编写泛型

### 2.1、泛型类

泛型类的出现最主要的动机之一是为了创建集合类。

我们先看一下只能持有单个对象的类。这个类明确指定其持有的对象的类型：

```java
class Automobile {}

public class Hodler {
	private Automobile a;
	public Holder(Automobile a) { this.a = a; }
	Automobile get() { return a; }
}
```

这个类的可复用性不高，它无法持有其它类型的对象。

在 Java 5 之前，我们可以让这个类直接持有 `Object` 类型的对象：

```java
public class ObjectHolder {
	private Object a;
	public ObjectHolder(Object a) { this.a = a; }
    public void set(Object a) { this.a = a; }
    public Object get() { return a; }

    public static void main(String[] args) {
        ObjectHolder h2 = new ObjectHolder(new Automobile());
        Automobile a = (Automobile)h2.get();
        h2.set("Not an Automobile");
        String s = (String)h2.get();
        h2.set(1); // 自动装箱为 Integer
        Integer x = (Integer)h2.get();
    }
}
```

现在，`ObjectHolder` 可以持有任何类型的对象，在上面的示例中，一个 `ObjectHolder` 先后持有了三种不同类型的对象。

一个集合中存储多种不同类型的对象的情况很少见，通常，我们只会在集合中存储同一种类型的对象。泛型的主要目的之一就是用来约定集合要存储什么类型的对象，并且通过编译器确保规约得以满足。

因此，我们先指定一个类型占位符：

```java
public class GenericHolder<T> {
    private T a;
    public GenericHolder() {}
    public void set(T a) { this.a = a; }
    public T get() { return a; }

    public static void main(String[] args) {
        GenericHolder<Automobile> h3 = new GenericHolder<Automobile>();
        h3.set(new Automobile()); // 此处有类型校验
        Automobile a = h3.get();  // 无需类型转换
        //- h3.set("Not an Automobile"); // 报错
        //- h3.set(1);  // 报错
    }
}
```

创建 `GenericHolder` 对象时，必须指明要持有的对象的类型，然后，`GenericHolder` 中只能存储该类型或其子类的对象。当调用 `get()` 取值就是正确的类型。

这就是Java泛型的核心概念：你只需告诉编译器要使用什么类型，剩下的细节交给它来处理。

在 Java 5 中，`GenericHolder<Automobile> h3 = new GenericHolder<Automobile>()` 这样的写法是必要的，但在 Java 7 中的写法为 `GenericHolder<Automobile> h3 = new GenericHolder<>()`，称为"钻石语法"。

编写泛型时，需要定义泛型类型`<T>`；
静态方法不能引用泛型类型`<T>`，必须定义其它类型（例如`<K>`） 来实现静态泛型方法；
泛型可以同时定义多种类型，例如：`Map<K,V>`。

### 2.2、泛型接口

泛型也可以引用于接口。例如 "生成器"，这是一种专门负责创建对象的类。实际上，这是工厂方法设计模式的一种应用。不过，当使用生成器创建新的，它不需要任何参数，而工厂方法一般需要参数。生成器无序额外的信息就知道如何创建新对象。

一般而言，一个生成器只定义一个方法，用于创建对象。例如 `java.util.function` 类库中的 `Supplier` 就是一个生成器，调用其 `get()` 获取对象。`get()` 是泛型方法，返回值为类型参数 `T`。

为了演示 `Supplier`，我们需要定义几个类。下面是个咖啡相关的继承体系：

```java
public class Coffee {
    private static long counter = 0;
    private final long id = counter++;

    @Override
    public String toString() {
        return getClass().getSimpleName() + " " + id;
    }
}

public class Latte extends Coffee {}

public class Mocha extends Coffee {}

public class Cappuccino extends Coffee {}

public class Americano extends Coffee {}

public class Breve extends Coffee {}
```

现在，我们可以编写一个类，实现 `Supplier<Coffee>` 接口，它能够随机生成不同类型的 `Coffee` 对象：

```java
public class CoffeeSupplier implements Supplier<Coffee>, Iterable<Coffee> {
    private Class<?>[] types = {Latte.class, Mocha.class,
            Cappuccino.class, Americano.class, Breve.class};
    private static Random rand = new Random(47);
    public CoffeeSupplier() {}
    private int size = 0;
    public CoffeeSupplier(int size) {
        this.size = size;
    }

    @Override
    public Coffee get() {
        try {
            return (Coffee) types[rand.nextInt(types.length)].newInstance();
        } catch (IllegalAccessException | InstantiationException e) {
            throw new RuntimeException(e);
        }
    }

    class CoffeeIterator implements Iterator<Coffee> {
        int count = size;
        @Override
        public boolean hasNext() {
            return count > 0;
        }
        @Override
        public Coffee next() {
            count--;
            return CoffeeSupplier.this.get();
        }

        @Override
        public void remove() {
            throw new UnsupportedOperationException();
        }
    }

    @Override
    public Iterator<Coffee> iterator() {
        return new CoffeeIterator();
    }

    public static void main(String[] args) {
        Stream.generate(new CoffeeSupplier())
                .limit(5)
                .forEach(System.out::println);
        for (Coffee c : new CoffeeSupplier(5)) {
            System.out.println(c);
        }
    }
}
```

```shell
Americano 0
Latte 1
Americano 2
Mocha 3
Mocha 4
Breve 5
Americano 6
Latte 7
Cappuccino 8
Cappuccino 9
```

参数化的 `Supplier` 接口确保 `get()` 返回值是参数的类型。`CoffeeSupplier` 同时还实现了 `Iterable` 接口，所以能用 `for-in` 语句。不同，它还需要知道何时终止循环，这正是第二个构造函数的作用。

下面是另一个实现 `Supplier<T>` 接口的例子，它负责生成 Fibonacci 数列：

```java
public class Fibonacci implements Supplier<Integer> {
    private int count = 0;
    @Override
    public Integer get() {
        return fib(count++);
    }
    private int fib(int n) {
        if (n < 2) return 1;
        return fib(n - 2) + fib(n - 1);
    }
    public static void main(String[] args) {
        Stream.generate(new Fibonacci())
                .limit(18)
                .map(n -> n + " ")
                .forEach(System.out::print);
    }
}
```

输出结果：

```java
1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987 1597 2584 
```

虽然我们在 `Fibonacci` 类的里里外外使用的都是 `int` 类型，但是其参数类型确实 `Integer`。这个例子引出了 Java 泛型的一个局限性：基本类型无法作为类型参数。不过 Java 5 具备自动装箱和拆箱的功能，可以很方便地在基本类型和相应的包装类之间进行转换。通过这个例子中 `Fibonacci` 类对 `int` 的使用，我们已经看到了这种效果。

如果还想更进一步，编写一个实现了 `Iterable` 的 `Fibnoacci` 生成器。我们的一个选择是重写这个类，令其实现 `Iterable` 接口。不过，你并不是总能拥有源代码的控制权，并且，除非必须这么做，否则，我们也不愿意重写一个类。而且我们还有另一种选择，就是创建一个 适配器 (Adapter) 来实现所需的接口，我们在前面介绍过这个设计模式。

有多种方法可以实现适配器。例如，可以通过继承来创建适配器类：

```java
public class IterableFibonacci extends Fibonacci implements Iterable<Integer> {

    private int n;

    public IterableFibonacci(int count) {
        this.n = count;
    }

    @Override
    public Iterator<Integer> iterator() {
        return new Iterator<Integer>() {
            @Override
            public boolean hasNext() {
                return n > 0;
            }

            @Override
            public Integer next() {
                n--;
                return IterableFibonacci.this.get();
            }

            @Override
            public void remove() {
                throw new UnsupportedOperationException();
            }
        };
    }
    public static void main(String[] args) {
        for (int i : new IterableFibonacci(18)) {
            System.out.print(i + " ");
        }
    }
}
```

输出结果：

```shell
1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987 1597 2584 
```

在 `for-in` 语句中使用 `IterableFibonacci`，必须在构造函数中提供一个边界值，这样 `hashNext()` 才知道何时返回 false，结束循环。

### 2.3、泛型方法

泛型方法独立于类而改变方法。作为准则，请"尽可能"使用泛型方法。通常将单个方法泛型化要比将这个类泛型化更清晰易懂。

如果方法是 `static` 的，则无法访问该类的泛型类型参数，因此，如果使用了泛型类型参数，则它必须是泛型方法。

要定义泛型方法，请将泛型参数列表放置在返回值之前，如下所示：

```java
public class GenericMethods {

    public <T> void f(T x) {
        System.out.println(x.getClass().getName());
    }

    public static void main(String[] args) {
        GenericMethods gm = new GenericMethods();
        gm.f("");
        gm.f(1);
        gm.f(1.0);
        gm.f(1.0F);
        gm.f('c');
        gm.f(gm);
    }
}
```

输出结果：

```shell
java.lang.String
java.lang.Integer
java.lang.Double
java.lang.Float
java.lang.Character
me.hireny.tutorial.java.java5.generic.coffee.GenericMethods
```

尽管可以同时对类及其方法进行参数化，但这里位将 `GenericMethods` 类参数化。只有方法 `f()` 具有类型参数，该参数由方法返回类型之前列表指示。

对于泛型类，必须在实例化该类时指定类型参数。使用泛型方法时，通常不需要指定参数类型，因为编译器会找出这些类型。这称为 类型参数推断。因此，对 `f()` 的调用看起来像普通的方法调用，并且 `f()` 看起来像被冲在了无数次一样。它甚至会接受 `GenericMethods` 类型的参数。

如果使用基本类型调用 `f()`，自动装箱就开始起作用，自动将基本类型包装在它们对应的包装类型中。


#### 变长参数和泛型方法

泛型方法和变长参数列表可以很好地共存：

```java
public class GenericVarargs {
    @SafeVarargs
    public static <T> List<T> makeList(T... args) {
        List<T> result = new ArrayList<>();
        for (T item : args) {
            result.add(item);
        }
        return result;
    }
    public static void main(String[] args) {
        List<String> ls = makeList("A");
        System.out.println(ls);
        ls = makeList("A", "B", "C");
        System.out.println(ls);
        ls = makeList(
                "ABCDEFFHIJKLMNOPQRSTUVWXYZ".split(""));
        System.out.println(ls);
    }
}
```

输出结果：

```shell
[A]
[A, B, C]
[A, B, C, D, E, F, F, H, I, J, K, L, M, N, O, P, Q, R, S, T, U, V, W, X, Y, Z]
```

此处显示的 `makeList()` 方法生产的功能与标准库的 `java.util.Arrays.asList()` 方法相同。

`@SafeVarargs` 注解保证我们不会对变长参数列表进行任何修改，这是正确的，因为我们只从中读取。如果没有此注解，编译器将无法直到这些并会发出警告。

#### 一个泛型的 Supplier

这是一个为任意具有无参构造方法的类生成 `Supplier` 的类。为了减少键入，它还包括一个用于生成 `BasicSupplier` 的泛型方法：

```java
public class BasicSupplier<T> implements Supplier<T> { 
    private Class<?> type;
    public BasicSupplier(Class<?> type) {
        this.type = type;
    }
    @Override
    public T get() {
        try {
            // Assumes type is a public class:
            return (T) type.newInstance();
        } catch (IllegalAccessException | InstantiationException e) {
            throw new RuntimeException(e);
        }
    }
    public static <T> Supplier<T> create(Class<T> type) {
        return new BasicSupplier<>(type);
    }
}
```

此类提供了产生以下对象的基本实现：

1. 是 `public` 的。因为 `BasicSupplier` 在单独的包中，所以相关的类必须具有 `public` 权限，而不仅仅是包级权限访问。
2. 具有无参构造方法。要创建一个这样的 `BasicSupplier` 对象，请调用 `create()` 方法，并将要生成类型的类型令牌传递给它。通用的 `create()` 方法提供了 `BasicSupplier.create(MyType.class)` 这种比较简洁的语法来代替较笨拙的 `new BasicSupplier<MyType>(MyType.class)`。

例如，这是一个具有无参构造方法的简单类：

```java
public class CountedObject {
    private static long counter = 0;
    private final long id = counter++;
    public long id() {
        return id;
    }
    @Override
    public String toString() {
        return "CountedObject " + id;
    }
}
```

`CountedObject` 类可以跟踪自身创建了多少实例，并通过 `toString()` 报告这些实例的数量。`BasicSupplier` 可以轻松地为 `CountedObject` 创建 `Supplier`：

```java
public class BasicSupplierDemo {
    public static void main(String[] args) {
    	Stream.generate(BasicSupplier.create(CountedObject.class))
                .limit(5)
                .forEach(System.out::println);
    }
}

```

输出结果：

```shell
CountedObject 0
CountedObject 1
CountedObject 2
CountedObject 3
CountedObject 4
```

泛型方法减少了产生 `Supplier` 对象所需要的代码量。Java 泛型强制传递 `Class` 对象，以便在 `create()` 方法中将其用于类型推断。

使用泛型时需要注意如下事项：

- 基本类型无法作为类型参数；
- 可用应用于内部类和匿名内部类；
- 在泛型代码内部，无法获取任何有关泛型参数类型的信息；
- 泛型不能显示的引用运行时类型的操作之中，例如转型，instanceof操作，new表达式等；

## 三、泛型擦拭


当你开始更深入地钻研泛型时，会发现有大量的东西初看起来是没有意义的。例如，尽管可以说 `ArrayList.class`，但不能说成 `ArrayList<Integer>.class`。考虑下面的情况：

```java
public class ErasedTypeEquivalence {
    public static void main(String[] args) {
        Class c1 = new ArrayList<String>().getClass();
        Class c2 = new ArrayList<Integer>().getClass();
        System.out.println(c1 == c2);
    }
}
```

输出结果：

```shell
true
```

`ArrayList<String>` 和 `ArrayList<Integer>` 应该是不同的类型。不同的类型会有不同的行为。例如，如果尝试向 `ArrayList<String>` 中放入一个 `Integer`，所得到的行为（失败）和向 `ArrayList<Integer>` 中放入一个 `Integer` 所得到的行为（成功）完全不同。然而上面的程序认为它们是相同的类型。

下面的例子是对该谜题的补充：

```java
class Frob {}
class Fnorkle {}
class Quark<Q> {}

class Particle<POSITION, MOMENTUM> {}
public class LostInformation {
    public static void main(String[] args) {
        List<Frob> list = new ArrayList<>();
        Map<Frob, Fnorkle> map = new HashMap<>();
        Quark<Fnorkle> quark = new Quark<>();
        Particle<Long, Double> p = new Particle<>();
        System.out.println(Arrays.toString(list.getClass().getTypeParameters()));
        System.out.println(Arrays.toString(map.getClass().getTypeParameters()));
        System.out.println(Arrays.toString(quark.getClass().getTypeParameters()));
        System.out.println(Arrays.toString(p.getClass().getTypeParameters()));
    }
}
```

输出结果：

```shell
[E]
[K, V]
[Q]
[POSITION, MOMENTUM]
```

根据 JDK 文档，`Class.getTypeParameters()` “返回一个 `TypeVariable` 对象数组，表示泛型声明中声明的类型参数...” 这暗示你可以发现这些参数类型。但是正如上例中输出所示，你只能看到用作参数占位符的标识符，这并非有用的信息。

残酷的现实是：

在泛型代码内部，无法获取任何有关泛型参数类型的信息。

因此，你可以知道如类型参数标识符和泛型边界这些信息，但无法得知实际的类型参数从而用来创建特定的实例。

Java 泛型是使用擦除实现的。这意味着当你在使用泛型时，任何具体的类型信息都被擦除了，你唯一知道的就是你在使用一个对象。因此，`List<String>` 和 `List<Integer>` 在运行时实际上是相同的类型。它们都被擦除成原生类型 `List`。


## 四、边界

边界（bounds）允许我们对泛型使用的参数类型施加约束。尽管这可以强制执行有关应用了泛型类型的规则，但潜在的更重要的效果是我们可以在绑定的类型中调用方法。

对于所有的数值类，比如 `Integer` 以及 `Double`，都是 `Number` 类的子类，而 `Number` 类定义了 `doubleValue()` 方法，所以所有的数值封装器都可以使用该方法。但编译器不知道我们试图创建的是只使用数值类型的 `Stats` 对象，所以 `doubleValue()` 方法对编译器来说是未知方法，因此在试图编译时以下泛型类会报错

```java
public class Stats<T> {

    private T[] numbers;

    public Stats(T[] numbers) {
        this.numbers = numbers;
    }
    public double average() {
        double sum = 0;
        for (T t : numbers) {
            //错误，因为并不是每种类型参数都包含 doubleValue 方法
            //需要我们主动告诉编译器我们传入的类型参数都是Number类的子类
            sum += t.doubleValue();
        }
        return sum / numbers.length;
    }

}
```

为了处理这种情况，需要为泛型类提供有界类型。在指定类型参数时，可以创建声明超类的上界，所有类型参数都必须派生自超类，通过关键字 `extends` 来声明限制。

这样，`T` 只能被 `Number` 类或其子类替代，阻止创建非数值类型的`Stats` 对象

```java
public class Stats<T extends Number> {
    private T[] numbers;
    public Stats(T[] numbers) {
        this.numbers = numbers;
    }
    public double average() {
        double sum = 0;
        for (T t : numbers) {
            sum += t.doubleValue();
        }
        return sum / numbers.length;
    }
}
```

```java
public class GenericMain {
    public static void main(String[] args) {
        Integer[] intNumbers = {1, 2, 3, 4};
        Stats<Integer> integerStats = new Stats<>(intNumbers);
        System.out.println("int numbers average: " + integerStats.average());

        System.out.println();

        Double[] doubleNumbers = {1.0, 2.0, 3.0, 4.0};
        Stats<Double> doubleStats = new Stats<>(doubleNumbers);
        System.out.println("double numbers average: " + doubleStats.average());
    }
}
```

输出结果：

```shell
int numbers average: 2.5

double numbers average: 2.5
```

此外，除了使用类作为边界外，边界也可以包含一个类和一个或多个接口。声明方式是：先指定类类型，再使用 & 运算符连接接口：

```java
interface HasColor {}
class Coord {}
interface Weight {}
class Solid<T extends Coord & HasColor & Weight> {}
```

## 五、通配符

### 5.1、无界通配符

为上一节的 `Stats` 泛型类添加一个比较 `Stats` 对象之间包含的数组的平均值是否相等的方法

```java
public class Stats<T extends Number> {
    ...
    public boolean sameAvg(Stats<T> ob) {
        return average() == ob.average();
    }

}
```

但在使用的过程中，你就会发现 `sameAvg` 方法给我们带来了极大的限制，因为 `sameAvg()` 方法的参数值泛型类型被指定为和被调用对象的参数类型是相同的

如下的调用方式会使编译器提示错误。因此，这种方式的适用范围很窄，无法得到泛型化的解决方案

```java
public class Test {
    public static void main(String[] args) {
        Integer[] intNumbers = {1, 2, 3, 4};
        Stats<Integer> integerStats = new Stats<>(intNumbers);

        Double[] doubleNumbers = {1.0, 2.0, 3.0, 4.0};
        Stats<Double> doubleStats = new Stats<>(doubleNumbers);
        // Error: saveAvg(Stats(java.lang.Integer) in Stats cannot be applied) to (java.lang.Double[])
        //integerStats.sameAvg(doubleNumbers);
    }
}
```

为了创建泛型化的 `saveAvg()` 方法，必须使用泛型的另一个特性：通配符参数。通配符参数由 "?" 指定，表示未知类型。在此，`Stats<?>` 和所有的 `Stats` 对象相匹配，允许任意两个`Stats` 对象比较他们的平均值。通配符只是简单地匹配所有有效的`Stats` 对象

```java
public boolean sameAvg(Stats<?> ob) {
	return average() == ob.average();
}
```

### 5.2、有界通配符

**上界通配符：**如果要为通配符建立上边界，可以使用如下所示的通配符表达式：

```java
<? extends E>
```

其中，E 是作为上界的类的名称。这是一条包含语句，即形成上界的类（即E）也包含于边界之内

> 上界：用 extends 关键字声明，表示参数化的类型可能是所指定的类型，或者是此类型的子类

**下界通配符：**如果要为通配符建立下边界，可以使用如下所示的通配符表达式：

```java
<? super E>
```

其中，只有 E 的超类是可接受参数。这是一条排除子句，即 E 指定的类不包含在内

> 下界：用 super 进行声明，表示参数化的类型可能是所指定的类型，或者是此类型的父类型，直至 Object

`extends` 可用于在返回类型限定，不能用于参数类型限定。
`super` 可用于参数类型限定，不能用于返回类型限定。
带有 `super` 超类型限定的通配符可以向泛型对象中写入，带有 `extends` 子类型限定的通配符可以向泛型对象读取。

### 5.3、PECS（Producer Extends Consumer Super）原则

PECS原则即Producer Extends Consumer Super原则

Producer Extends：如果你需要一个只读List，用它来`produce T`，那么使用`? extends T`。
Consumer Super：如果你需要一个只写List，用它来`consume T`，那么使用`? super T`。

```java
public class Example {
    public static void main(String[] args) {
        List<? extends Fruit> fruits = new ArrayList<>();
        // ? extends Fruit表示的是Fruit及其子类

        // Compile Error: can't add any type of object:
		//fruits.add(new Apple());
		//fruits.add(new Orange());
		//fruits.add(new Fruit());
		//fruits.add(new Object());
        fruits.add(null);   // Legal but uninteresting
    }
}
```

**Compile Error:can't add any type of object:**从编译器的角度去考虑。因为`List<? extends Fruit> fruits`它自身可以有多种含义：

```java
List<? extends Fruit> fruits = new ArrayList<Fruit>();
List<? extends Fruit> fruits = new ArrayList<Apple>();
List<? extends Fruit> fruits = new ArrayList<Orange>();
// 这里Apple和Orange都是Fruit子类
```

当我们尝试`add`一个`Apple`的时候，`fruits`可能指向`new ArrayList()`;
当我们尝试`add`一个`Orange`的时候，`fruits`可能指向`new ArrayList()`;
当我们尝试`add`一个`Fruit`的时候，这个`Fruit`可以是任何类型的`Fruit`，
而`fruits`可能只想某种特定类型的`Fruit`，编译器无法识别所以会报错。

所以对于实现了`<? extends T>` 的集合类只能将它视为 `Producer` 向外提供元素（只能读），而不能作为`Consumer`来向外获取元素。

```java
/**
 * 对于实现了 <? extends T> 的集合类只能将它视为 Producer 向外提供(get)元素，
 * 而不能作为 Consumer 来对外获取(add)元素
 */
public class GenericReading {
    private List<Apple> apples = Arrays.asList(new Apple());
    private List<Fruit> fruits = Arrays.asList(new Fruit());
    private class Reader<T> {
        // Reader<T> 是自定义的泛型类

        T readExact(List<? extends T> list) {   // 使用通配符来解决这个问题
            // ? extends T 表示 T 及 T 的子类
            return list.get(0);
        }
    }
    @Test
    public void test() {
        Reader<Fruit> fruitReader = new Reader<>();
        //Fruit f=fruitReader.readExact(apples);
        // 使用 readExact(List<T> list)
        // Errors: List<Fruit> cannot be applied to List<Apple>.

        Fruit f=fruitReader.readExact(apples);//正确
        System.out.println(f);
    }
}
```

```java
/**
 *
 * 使用super的坏处是以后不能get容器里面的元素了，
 * 原因很简单，我们继续从编译器的角度考虑这个问题，
 * 对于List<? super Apple> list，它可以有下面几种含义：
 * List<? super Apple> list = new ArrayList<Apple>();
 * List<? super Apple> list = new ArrayList<Fruit>();
 * List<? super Apple> list = new ArrayList<Object>();
 * 当我们尝试通过list来get一个Apple的时候，可能会get得到一个Fruit，这个Fruit可以是Orange等其他类型的Fruit。
 */
public class GenericWriting {
    private List<Apple> apples = new ArrayList<>();
    private List<Orange> oranges = new ArrayList<>();
    private List<Fruit> fruits = new ArrayList<>();
    <T> void writeExact(List<T> list, T item) {
        list.add(item);
    }
    <T> void writeWithWildcard(List<? super T> list, T item) {
        list.add(item);
    }
    void func1() {
        writeExact(apples, new Apple());
        writeExact(fruits, new Apple());
    }
    void func2() {
        writeWithWildcard(apples, new Apple());
        writeWithWildcard(fruits, new Orange());
    }
    @Test
    public void test() {
        func1();
        func2();
    }
}
```

JDK 8 Collections.copy()源码

```java
public static <T> void copy(List<? super T> dest, List<? extends T> src) {
	int srcSize = src.size();
	if (srcSize > dest.size())
		throw new IndexOutOfBoundsException("Source does not fit in dest");

	if (srcSize < COPY_THRESHOLD ||
		(src instanceof RandomAccess && dest instanceof RandomAccess)) {
		for (int i=0; i<srcSize; i++)
			dest.set(i, src.get(i));
	} else {
		ListIterator<? super T> di=dest.listIterator();
		ListIterator<? extends T> si=src.listIterator();
		for (int i=0; i<srcSize; i++) {
			di.next();
			di.set(si.next());
		}
	}
}
```


## 六、泛型限制

### 6.1、模糊性错误

看如下例子

对泛型类 User< T, K > 而言，声明了两个泛型类参数：T 和 K。在类中试图根据类型参数的不同重载 set() 方法。这看起来没什么问题，可编译器会报错

```java
public class User<T, K> {
    //重载错误
    public void set(T t) {
        
    }
	//重载错误
    public void set(K k) {

    }
}
```

首先，当声明 `User` 对象时，`T` 和 `K` 实际上不需要一定是不同的类型，以下的两种写法都是正确的

```java
public class GenericMain {
    public static void main(String[] args) {
        User<String, Integer> stringIntegerUser = new User<>();
        User<String, String> stringStringUser = new User<>();
    }
}
```

对于第二种情况，T 和 K 都将被 String 替换，这使得 set() 方法的两个版本完全相同，所以会导致重载失败。
此外，对 set() 方法的类型擦除会使两个版本都变为如下形式：

```java
public void set(Object o) {}
```

一样会导致重载失败

### 6.2、不能实例化类型参数

不能创建类型参数的实例。因为编译器不知道创建哪种类型的对象，T 只是一个占位符

```java
public class User<T> {
    private T t;
    public User() {
        //错误
        t = new T();
    }
}
```

### 6.3、对静态成员的限制

静态成员不能使用在类中声明的类型参数，但是可以声明静态的泛型方法

```java
public class User<T> {
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

### 6.4、对泛型数组的限制

不能实例化元素类型为类型参数的数组，但是可以将数组指向类型兼容的数组的引用：

```java
public class User<T> {
    private T[] values;
    public User(T[] values) {
        //错误，不能实例化元素类型为类型参数的数组
        this.values = new T[5];
        //正确，可以将values 指向类型兼容的数组的引用
        this.values = values;
    }
}
```

### 6.5、对泛型异常的限制

泛型类不能扩展 `Throwable`,意味着不能创建泛型异常类：

```java
// Error:Generic class may not extend 'java.lang.Throwable'
public CustomException<E> extends Exception {}
```
