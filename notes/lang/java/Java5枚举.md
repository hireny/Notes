- 枚举类
- 枚举类成员变量，方法，构造器
- 实现接口
- 包含抽象方法的枚举类


# 枚举

## 概述

枚举（Enum）是在Java 1.5中添加的新特性，通过关键字`enum`来定义枚举类。枚举类是一种特殊类，它和普通类一个可以使用构造器、定义成员变量和方法，也能实现一个或多个接口，但枚举类不能继承其它类。它有以下几个特点：

- 定义的 `enum` 类型总是继承自 `java.lang.Enum`，且无法被继承
- 只能定义出 `enum` 的实例，而无法通过 `new` 操作符创建 `enum` 的实例
- 定义的每个实例都是引用类型的唯一实例
- 可以将 `enum` 类型用于 `switch` 语句


```java
public enum  Color {
    RED, GREEN, BLACK, BLUE;
}
public void main(String[] args) {
	for (Color color : Color.values()) {
		System.out.println(color);
	}
}
```

输出结果为：

```java
RED
GREEN
BLACK
BLUE
```

反编译出 `java` 文件大概如下：

```java
// 继承自Enum，final修饰，无法被继承
public final class Color extends Enum {
	// 为了避免，返回的数组修改，而引起内部values值的改变，返回的是原数组的副本
	public static Color[] values() {
		return (Color[]) $VALUES.clone();
	}
	// 按名字获取枚举实例
	public static Color valueOf(String name) {
		return (Color) Enum.valueOf(em / Color, name);
	}
	// private构造方法，确保外部无法调用new操作符:
    private Color() {}
    // 每个实例均为全局唯一:
    public static final Color RED;
    public static final Color GREEN;
    public static final Color BLACK;
    public static final Color BLUE;
    //
    public static final Color $VALUES[];
    
    // 静态域初始化，说明在类加载的cinit阶段就会被实例化，jvm能够保证类加载过程的线性安全
    static {
    	RED = new Color("RED", 0);
    	GREEN = new Color("GREEN", 1);
    	BLACK = new Color("BLACK", 2);
    	BLUE = new Color("BLUE", 3);
    	$VALUES = (new Color[]{
    		RED, GREEN, BLACK, BLUE
    	});
    }
}
```

所以，使用 `enum` 关键字编写的类在反编译后与普通的`class`并没有什么区别。

## Enum成员变量和方法分析

```java
public abstract class Enum<E extends Enum<E>> implements Comparable<E>, Serializable {
	private final String name;
	public final String name() {}
	private final int ordinal;
	public final int ordinal() {}
	protected Enum(String name, int ordinal) {}
	public String toString() {}
	public final boolean equals(Object other) {}
	public final int hashCode() {}
	protected final Object clone() throws CloneNotSupportedException {}
	public final int compareTo(E o) {}
	public final Class<E> getDeclaringClass() {}
	public static <T extends Enum<T>> T valueOf(Class<T> enumType, String name) {}
	protected final void finalize() { }
	private void readObject(ObjectInputStream in) throws IOException,ClassNotFoundException {}
}
```
分析一下 `Enum` 类中的方法：

**1.name()**  

返回常量名，例如：

```java
String s = Color.RED.name();	// "RED"
```

**2.ordinal()**  

返回定义的常量的顺序，从0开始计数，例如：

```java
int n = Color.BLACK.ordinal();	//  2
```

Enum类实现了 `Comparable` 接口，表明它是支持排序的，可以通过 `Collections.sort` 进行自动排序，实现了 `public final int compareTo(E o)` 接口，方法定义为 `final` 且其实现依赖的 `ordinal` 字段也是 `final` 类型，说明它只能根据 `ordinal` 排序，排序规则不可变