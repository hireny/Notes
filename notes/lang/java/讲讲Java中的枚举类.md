当进行编程开发时，需要一组固定的常量来表示需要的类型，如使用一组 `int` 常量表示一周的日期：

```java
public class Week {
    public static final int MONDAY = 1;
    public static final int TUESDAY = 2;
    public static final int WEDNESDAY = 3;
    public static final int THURSDAY = 4;
    public static final int FIRDAY = 5;
    public static final int SATURDAY = 6;
    public static final int SUNDAY = 7;
}
```

这种方法称作 `int` 枚举模式。接下来设计一个使用日期的方法：

```java
public static boolean alarm(LocalTime time, int[] weeks) {
    ......
}
```

上述设置闹铃的方法中，第二个参数是传入日期数组，在 `weeks` 数组中的每个 `int` 值都必须在 `1~7` 之间，但因为传入的 `int` 值可以是任何值，不具有类型安全性，也几乎没有描述性可言，且编译时也不会提出任何警告；而且需要遍历 `Week` 类中所有表示星期的值的方式也没有。当然，这种设置常量的类型也不仅局限在 `int` 类型中，也可以使用 `String` 设置常量，被称为 `String` 枚举模式。但是，这种使用 `int`、`String` 或其它类型设置的枚举模式不是太可靠。

## 枚举

在这种情况下，Java 在 1.5 版本中引入枚举类型来解决 `int` 和 `String` 枚举模式所带有的缺点。使用 `enum` 定义的枚举类，是 `Object` 的子类，继承自 `java.lang.Enum<E extends Enum<E>>` 抽象类，不能继承其它类。

```java
public abstract class Enum<E extends Enum<E>>
        implements Comparable<E>, Serializable {
    ......
}
```

接下来，将上面的 `Week` 类重新编写：

```java
public enum  Week {
    MONDAY,TUESDAY,WEDNESDAY,THURSDAY,FIRDAY,SATURDAY,SUNDAY;
}
```

在以参数方式传入方法：

```java
public static boolean alarm(LocalTime time, Week[] weeks) {
    ......
}
```

重写编写后，`Week[]` 作为第二个参数，传入的每个 `Week` 值都是 `Week` 枚举类中的有效值之一，保证了编译时的类型安全。

## 原理

但其实，在 `Week` 枚举类中设置的枚举值，如`MONDAY`、`TUESDAY` ... `SUNDAY` 其实本质上是 `int` 值。

```java
public final class Week extends Enum {
    public static final Week MONDAY;
    public static final Week TUESDAY;
    public static final Week WEDNESDAY;
    public static final Week THURSDAY;
    public static final Week FIRDAY;
    public static final Week SATURDAY;
    public static final Week SUNDAY;
    private Week() {...}
    static {...}
    ......
}
```

代码编译后会获得使用 `public static final` 修饰的枚举常量，且通过 `static {}` 代码块为每个枚举常量赋值，之后，因为没有公共的构造器，不能通过 `new` 的方式创建实例，因此只有声明过的枚举常量。


## 特性

枚举类型除了完善之前的 `int` 或 `String` 枚举模式之外，继承自 `java.lang.Enum<E extends Enum<E>>` 的枚举类还可以使用 `Enum` 抽象类提供的 `value()` 方法返回 `enum` 实例的数组且元素严格保持其声明时的顺序：

```java
Arrays.toString(Week.values());
/**
 * [MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FIRDAY, SATURDAY, SUNDAY]
 */
```

提供的 `ordinal()` 方法和 `name()` 方法可以获取 `enum` 实例声明时的顺序（从 0 开始）和实例的常量名：

```java
for (Week value : Week.values()) {
    System.out.println("Week 枚举中第" + value.ordinal() + "个常量名是：" + value.name() );
}
/**
 * Week 枚举中第0个常量名是：MONDAY
 * Week 枚举中第1个常量名是：TUESDAY
 * Week 枚举中第2个常量名是：WEDNESDAY
 * Week 枚举中第3个常量名是：THURSDAY
 * Week 枚举中第4个常量名是：FIRDAY
 * Week 枚举中第5个常量名是：SATURDAY
 * Week 枚举中第6个常量名是：SUNDAY
 */
```

但是星期的枚举天生就与 `int` 值相关联，但通过 `ordinal()` 方法返回的只是每个枚举常量在类型中的数字位置。而不是我们需要的关于星期几的数字，如星期一要返回 `1` 这样的数字。这样，我们可以将相关联的值保存在一个实例域中：

```java
public enum Week {
    MONDAY(1),TUESDAY(2),WEDNESDAY(3),
    THURSDAY(4),FIRDAY(5),
    SATURDAY(6),SUNDAY(7);
    private final int number;
    Week(int number) {
        this.number = number
    }
    public int getNumber() {
        return number;
    }
}
```

`Enum` 规范中谈及 `ordinal()` 方法是用于 `EnumSet` 和 `EnumMap` 基于枚举的数据结构的。因此，使用时要避免使用 `ordinal()` 方法。

如果只有常量的名称，就可以使用 `valueOf(String name)` 将常量的名称转换为相应的 `enum` 实例，如果不存在给定的常量名称，将会抛出异常。

由于 `java.lang.Enum<E extends Enum<E>>` 实现了 `Comparable<E>` 和 `Serializable` 接口，因此支持排序并且可以序列化。而且在 `enum` 实例上调用 `getDeclaringClass()` 方法，可以获取其枚举的实例所属的枚举类。

除了不能使用 `extends` 实现继承外，基本上可以将枚举类看作一个常规类，允许添加任意的方法和域，并实现任意的接口。

### 方法添加

就像上面的 `Week` 枚举类，可以提供一个用于描述 `Week` 实例的方法来增强枚举类型。

```java
public enum Week {
    MONDAY("星期一"),TUESDAY("星期二"),WEDNESDAY("星期三"),
    THURSDAY("星期四"),FIRDAY("星期五"),
    SATURDAY("星期六"),SUNDAY("星期日");
    private final String desc;
    Week(String desc) {
        this.desc = desc;
    }
    public String getDesc() {
        return desc;
    }
}
```

因为枚举的不可变性，声明的实例域都应该为 `final`的，可以将 `private` 设为 `public`，但不建议。这样在编写一个带有数据并将数据保存在域中的构造器将数据与枚举常量关联起来就可以了。

使用枚举还可以将不同的行为与每个枚举常量关联起来，如在枚举类型中声明一个抽象方法，并在特定于常量的类主体中，用具体的方法覆盖每个常量的抽象方法。这种方法被称作 **特定于常量的方法实现** 。下面使用枚举来编写一个四则运算：

```java
public enum Operation {
    PLUS("+") {
        @Override
        public double apply(double x, double y) {
            return x + y;
        }
    },
    MINUS("-") {
        @Override
        public double apply(double x, double y) {
            return x - y;
        }
    },
    TIMES("*") {
        @Override
        public double apply(double x, double y) {
            return x * y;
        }
    },
    DIVIDE("/") {
        @Override
        public double apply(double x, double y) {
            return x / y;
        }
    };
    private final  String symbol;
    Operation(String symbol) { this.symbol = symbol; }
    @Override
    public String toString() { return symbol; }
    public abstract double apply(double x, double y);
}
```

通过这种方式可以消除烦人的 `if/else` 与 `switch` 语句。下面给出一个使用 `switch` 方法来实现的四则运算：

```java
public enum Operation {
    PLUS,MINUS,TIMES("*"),DIVIDE("/");
    private final  String symbol;
    Operation(String symbol) { this.symbol = symbol; }
    @Override
    public String toString() { return symbol; }
    public double apply(double x, double y) {
        switch (this) {
            case PLUS: return x + y;
            case MINUS: return x - y;
            case TIMES: return x * y;
            case DIVIDE: return x / y;
        }
        throw new AssertionError("Unknown op:" + this);
    };
}
```

与上面实现的方式相比，使用 `if/else` 或 `switch` 的判断方式要繁琐的多。

###  接口实现

因为创建的枚举类都会继承自 `java.lang.Enum<E extends Enum<E>>`，在 Java 单继承体系中就不能在继承其它的类了，但是可以实现任意的接口。如上面在枚举类中抽象方法移到接口中：

```java
public enum Operation implements BiFunction<Double, Double, Double> {
    PLUS("+") {
        @Override
        public Double apply(Double x, Double y) {
            return x + y;
        }
    },
    MINUS("-") {
        @Override
        public Double apply(Double x, Double y) {
            return x - y;
        }
    },
    TIMES("*") {
        @Override
        public Double apply(Double x, Double y) {
            return x * y;
        }
    },
    DIVIDE("/") {
        @Override
        public Double apply(Double x, Double y) {
            return x / y;
        }
    };
    private final  String symbol;
    Operation(String symbol) { this.symbol = symbol; }
    @Override
    public String toString() { return symbol; }
}
```

因为接口是可扩展的，就可以定义另一个实现该接口的枚举类，并用这个新类型的实例代替 `Operation` 类型。如下所示：

```java
public enum ExtendedOperation implements BiFunction<Double, Double, Double> {
    EXP("^") {
        @Override
        public Double apply(Double x, Double y) {
            return Math.pow(x, y);
        }
    },
    REMAINDER("%") {
        @Override
        public Double apply(Double x, Double y) {
            return x % y;
        }
    };
    private final String symbol;
    ExtendedOperation(String symbol) { this.symbol = symbol; }

    public String getSymbol() { return symbol; }

    @Override
    public String toString() { return symbol; }
}
```

如此，无法编写可扩展的枚举类型，可以通过实现接口来实现扩展。

## 集合

java 中有专门为枚举提供的 `EnumSet` 和 `EnumMap` 数据结构。下面简单介绍一下它们。

### EnumSet

`EnumSet` 类是 `Set` 集合的子类，用于添加不重复的值。在应用于枚举类时，有效地表示从单个枚举类型中提取的多个值的多个集合。如上面的 `Week` 枚举，当用户传入一个值，我们需要判断是否是 `EnumSet` 中的值：

```java
EnumSet<Week> weeks = EnumSet.of(Week.MONDAY, Week.TUESDAY, Week.WEDNESDAY,Week.THURSDAY, Week.FIRDAY);
boolean isWork = weeks.contains(Week.SATURDAY);
```

这里设置的 `weeks` 中是工作的时间，只有每周六、日放假，使用 `weeks.contains()` 判断是否是工作日。

### EnumMap

`EnumMap` 是专门为映射枚举而开发的 `Map` 映射表，它要求键必须是 `Enum` 类型，我们可以将上面为`Week` 添加了一个描述的方法修改为 `EnumMap` 映射表的形式：

```java
EnumMap<Week, String> map = new EnumMap<Week, String>(Week.class);
map.put(Week.MONDAY, "星期一");
map.put(Week.TUESDAY, "星期二");
map.put(Week.WEDNESDAY, "星期三");
map.put(Week.THURSDAY, "星期四");
map.put(Week.FIRDAY, "星期五");
map.put(Week.SATURDAY, "星期六");
map.put(Week.SUNDAY, "星期日");
```

这样，我们可以通过 `EnumMap` 提供的 API 快速查找 `Week` 枚举对应的值。

## 总结

因此，当使用一组固定的常量表示合法值，就可以使用枚举类型来表示，且通过 `EnumSet` 和 `EnumMap` 和 `Enum` 自带的方法，可以很方便的操作枚举类型，而且封装性良好。因此，在项目中遇到有限常量的情况下，可以试着使用枚举。