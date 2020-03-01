# 八、泛型

Java泛型设计原则：只要在编译时期没有出现警告，那么运行时期就不会出现ClassCastException异常。

泛型：把类型明确的工作推迟到创建对象或调用方法的时候才去明确的特殊的类型。

参数化类型：

- 把类型当作是参数一样传递
- `<数据类型>` 只能是引用类型

相关术语：

- `ArrayList<E>` 中的E称为类型参数变量
- `ArrayList<Integer>` 中的 Integer 称为实际类型参数
- 整个称为 `ArrayList<E>` 泛型类型
- 整个 `ArrayList<Integer>` 称为参数化的类型 `ParameterizedType`

## 为什么需要泛型

早期Java是使用Object来代表任意类型的，但是向下转型有强转的问题，这样程序就不太安全

首先，我们来试想一下：没有泛型，集合会怎么样

- `Collection`、`Map` 集合对元素的类型是没有任何限制的。本来我的Collection集合装载的是全部的Dog对象，但是外边把Cat对象存储到集合中，是没有任何语法错误的。
- 把对象扔进集合中，集合是不知道元素的类型是什么的，仅仅知道是Object。因此在get() 的时候，返回的是 Object。外边获取该独享，还需要强制转换

有了泛型以后：

- 代码更加简洁【不用强制转换】
- 程序更加健壮【只要编译时期没有警告，那么运行时期就不会出现ClassCastException异常】
- 可读性和稳定性【在编写集合的时候，就限定了类型】

## 使用泛型

使用泛型时，把泛型参数 `<T>` 替换为需要的 `class` 类型，例如：`ArrayList<String>`，`ArrayList<Number>` 等；
可以省略编译器能自动推断出的类型，例如：`List<String> list = new ArrayList<>();`；
不指定泛型参数类型时，编译器会给出警告，且只能将`<T>`视为`Object`类型；
可以在接口中定义泛型类型，实现此接口的类必须实现正确的泛型类型。

## 编写泛型

编写泛型时，需要定义泛型类型`<T>`；
静态方法不能引用泛型类型`<T>`，必须定义其它类型（例如`<K>`） 来实现静态泛型方法；
泛型可以同时定义多种类型，例如：`Map<K,V>`。

```java
public class Box<T> {
    // T stands for "Type"
    private T t;
    public Box(T t) {
    	this.t = t;
    }
    public void set(T t) { this.t = t; }
    public T get() { return t; }
    
    // 该静态方法的泛型<T>与类BOx<T>的泛型没有任何关系。
    // 对于静态方法，我们可以单独改写为"泛型"方法，只需要使用另一个类型即可。
    public static <T> Object getBox(T t) {
    	return new Box(t);
    }
}
```

## 擦拭法

Java语言的泛型实现方式是擦拭法（Type Erasure）。
所谓擦拭法是指，虚拟机对泛型其实一无所知，所有的工作都是编译器做的。
例如，我们编写了一个泛型类`Pair<T>`，这是编译器看到的代码：

```java
public class Pair<T> {
	private T first;
	private T last;
	public Pair(T first, T last) {
		this.first = first;
		this.last = last;
	}
	public T getFirst() {
		return first;
	}
	public T getLast() {
		return last;
	}
}
```

而虚拟机根本不知道泛型。这是虚拟机执行的代码：

```java
public class Pair {
	private Object first;
	private Object last;
	public Pair(Object first, Object last) {
		this.first = first;
		this.last = last;
	}
	public Object getFirst() {
		return first;
	}
	public Object getLast() {
		return last;
	}
}
```

因此，Java使用擦拭法实现泛型，导致了：
- 编译器会把类型`<T>`视为`Object`
- 编译器根据`<T>`实现安全的强制转型

使用泛型的时候，我们编写的代码也是编译器看到的代码：

```java
Pair<String> p = new Pair<>("Hello","world");
String first = p.getFirst();
String last = p.getLast();
```

而虚拟机执行的代码并没有泛型：

```java
Pair p = new Pair("Hello","world");
String first = (String) p.getFirst();
String last = (String) p.getLast();
```

所以，Java泛型是由编译器在编译时实行的，编译器内部永远把所有类型`<T>`视为`Object`处理，但是，在需要转型的时候，编译器会根据`T`的类型自动为我们实行安全地强制转型。

泛型的局限：

1. `<T>`不能是基本类型，例如`int`,因为实际类型是`Object`，`Object`类型无法持有基本类型
2. 无法取得带泛型的`Class`
3. 无法判断带泛型的`Class`
4. 不能实例化`T`类型

- [10 道 Java 泛型面试题](https://cloud.tencent.com/developer/article/1033693)