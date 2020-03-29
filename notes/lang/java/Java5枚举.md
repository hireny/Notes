# 枚举

- 一、定义
- 二、原理分析
- 三、枚举的方法
- 四、枚举的特性
- 五、EnumSet与EnumMap
- 六、枚举的应用


## 一、定义

枚举（Enum）是在Java 1.5中添加的新特性，通过关键字`enum`来定义枚举类。枚举类是一种特殊类，它和普通类一个可以使用构造器、定义成员变量和方法，也能实现一个或多个接口，但枚举类不能继承其它类。它有以下几个特点：

- 定义的 `enum` 类型总是继承自 `java.lang.Enum`，且无法被继承
- 只能定义出 `enum` 的实例，而无法通过 `new` 操作符创建 `enum` 的实例
- 定义的每个实例都是引用类型的唯一实例
- 可以将 `enum` 类型用于 `switch` 语句


## 二、原理分析


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

从反编译的类中，可以看出，我们使用 `enum` 关键字编写的类，在编译阶段编译器会自动帮我们生成一份真正的JVM中运行的代码。

`Enum`类接受一个继承自 `Enum` 的泛型（在反编译Java文件中没有体现泛型是因为泛型在编译阶段就会进行类型擦除，替换为具体的实现）。

从反编译的 `Color` 类中可以看出，在`enum`关键字的类中，第一行（准确的说是第一个分号前）定义的变量，都会生成一个`Color`实例，且它是在静态域中进行初始化的，而静态域在类加载阶段的`cinit`中进行初始化，所以枚举对象是线程安全的，由JVM来保证。

生成的枚举类有 `Color $VALUES[];`成员变量，外部可以通过`values()`方法获取当前枚举类的所有实例对象。


## 三、枚举的方法

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

**1.values()**  

返回 `enum` 实例的数组，而且该数组中的元素严格保持在 `enum` 中声明的顺序，例如：

```java
Color.values();
//RED
//GREEN
//BLACK
//BLUE
//[Lme.xx.xx.Color;@5b464ce8
```

**2.name()**  

返回常量名，例如：

```java
String s = Color.RED.name();	// "RED"
```

**3.ordinal()**  

返回定义的常量的顺序，从0开始计数，例如：

```java
int n = Color.BLACK.ordinal();	//  2
```

**4.compareTo(E o)**  

用于比较相同类型的两个枚举常量的序数值，例如：

```java
int n = Color.BLACK.compareTo(Color.RED);	// 2
```

Enum类实现了 `Comparable` 接口，表明它是支持排序的，可以通过 `Collections.sort` 进行自动排序，实现了 `public final int compareTo(E o)` 接口，方法定义为 `final` 且其实现依赖的 `ordinal` 字段也是 `final` 类型，说明它只能根据 `ordinal` 排序，排序规则不可变

```java
public final int compareTo(E o) {
	Enum<?> other = (Enum<?>)o;
	Enum<E> self = this;
	if (self.getClass() != other.getClass() && // optimization
		self.getDeclaringClass() != other.getDeclaringClass())
 		throw new ClassCastException();
	return self.ordinal - other.ordinal;
}
```

**5.equals(Object other)**  

用于比较两个对象的相等性，例如：

```java
boolean n = Color.RED.equals(Color.BLACK);	// false
```

**6.getDeclaringClass()**  

返回实例所属的 `enum` 类型，例如：

```java
Color.RED.getDeclaringClass();	// class me.xx.xx.Color
```

## 四、枚举的特性

### 添加方法

除了不能继承自一个 `enum` 之外，我们可以将 `enum` 看作一个常规的类。

```java
public enum OzWitch {
    // Instances must be defined first, before methods:
    WEST("Miss Gulch, aka the Wicked Witch of the West"),
    NORTH("Glinda, the Good Witch of the North"),
    EAST("Wicked Witch of the East, wearer of the Ruby " +
            "Slippers, crushed by Dorothy's house"),
    SOUTH("Good by inference, but missing");
    private String description;
    private OzWitch(String description) {
        this.description = description;
    }
    public String getDescription() {
        return description;
    }
    public static void main(String[] args) {
        for (OzWitch witch : OzWitch.values()) {
            System.out.println(witch + ": " + witch.getDescription());
        }
    }
}
```

输出为：

```shell
WEST: Miss Gulch, aka the Wicked Witch of the West
NORTH: Glinda, the Good Witch of the North
EAST: Wicked Witch of the East, wearer of the Ruby Slippers, crushed by Dorothy's house
SOUTH: Good by inference, but missing
```

在这个例子中，虽然我们将 `enum` 的构造器声明为 `private`，但对于它的访问性而言，其实并没有什么变化，因为（即使不声明为 private）我们只能在 enum 定义的内部使用其构造器创建 enum 实例。一旦 enum 的定义结束，编译器就不允许我们再使用其构造器来创建任何实例了。

### 实现接口

所有的 `enum` 都继承自 `java.lang.Enum` 类。由于 Java 不支持多重继承，所以你的 `enum` 不能再继承其它类：

```java
enum NotPossible extends Pet { ... // Won't work
```

然而，在我们创建一个新的 `enum` 时，可以同时实现一个或多个接口：

```java
enum CartoonCharacter implements Supplier<CartoonCharacter> {
    SLAPPY, SPANKY, PUNCHY,
    SILLY, BOUNCY, NUTTY, BOB;
    private Random rand = new Random(47);
    @Override
    public CartoonCharacter get() {
        return values()[rand.nextInt(values().length)];
    }
}
public class EnumImplementation {
    public static <T> void printNext(Supplier<T> rg) {
        System.out.print(rg.get() + ", ");
    }

    public static void main(String[] args) {
        // Choose any instance;
        CartoonCharacter cc = CartoonCharacter.BOB;
        for (int i = 0; i < 10; i++) {
            printNext(cc);
        }
    }
}
```

输出为：

```shell
BOB, PUNCHY, BOB, SPANKY, NUTTY, PUNCHY, SLAPPY, NUTTY, NUTTY, SLAPPY, 
```

这个结果有点奇怪，不过你必须要有一个 enum 实例才能调用其上的方法。现在，在任何接受 Supplier 参数的方法中，例如 printNext()，都可以使用 CartoonCharacter。

## 五、EnumSet与EnumMap

### 使用EnumSet替代Flags

`Set`是一种集合，只能向其中添加不重复的对象。当然，`enum`中成员都是唯一的，所以`enum`也具有集合的行为。不过，不能从`enum`中删除或添加元素，所以它没有什么作用。JDK 5引入`EnumSet`，专为枚举设计的集合类，以替代传统的基于 `int` 的 "位标志"。

`EnumSet` 的设计充分考虑到了速度因素，因为它必须与非常高效的 bit 标志相竞争（其操作与 `HashSet` 相比，非常快），就其内部而言，它（可能）就是将一个 `long` 值作为比特向量，所以 `EnumSet` 非常快速高效。使用 `EnumSet` 的优点是，它在说明一个二进制位是否存在时，具有更好的表达能力，并且无需担心性能。

`EnumSet` 中的元素必须来自一个 `enum`，下面的 `enum` 表示在一座大楼中，警告传感器的安放位置：

```java
public enum AlarmPoints {
    STAIR1, STAIR2, LOBBY, OFFICE1, OFFICE2, OFFICE3,
    OFFICE4, BATHROOM, UTILITY, KITCHEN
}
```

然后，我们用 `EnumSet` 来跟踪报警器的状态：

```java
public class EnumSets {
    public static void main(String[] args) {
        // EnumSet.noneOf()创建一个空的Set
        EnumSet<AlarmPoints> points =
                EnumSet.noneOf(AlarmPoints.class); // Empty
        points.add(BATHROOM);
        System.out.println(points);
        points.addAll(
                EnumSet.of(STAIR1, STAIR2, KITCHEN));
        System.out.println(points);
        points = EnumSet.allOf(AlarmPoints.class);
        points.removeAll(
                EnumSet.of(STAIR1, STAIR2, KITCHEN));
        System.out.println(points);
        points.removeAll(
                EnumSet.range(OFFICE1, OFFICE4));
        System.out.println(points);
        points = EnumSet.complementOf(points);
        System.out.println(points);
    }
}
```

输出为：

```shell
[BATHROOM]
[STAIR1, STAIR2, BATHROOM, KITCHEN]
[LOBBY, OFFICE1, OFFICE2, OFFICE3, OFFICE4, BATHROOM, UTILITY]
[LOBBY, BATHROOM, UTILITY]
[STAIR1, STAIR2, OFFICE1, OFFICE2, OFFICE3, OFFICE4, KITCHEN]
```

`EnumSet`的基础是 `long`，一个 `long` 值有64位，而一个`enum`实例只需要一位bit表示其是否存在。也就是说，在不超过一个 `long` 的表达能力的情况下，你的`EnumSet`可以应用于最多不超过 64 个元素的 `enum`。如果`enum`超过了64个元素会发生什么呢？

```java
public class BigEnumSet {
    enum Big { A0, A1, A2, A3, A4, A5, A6, A7, A8, A9,
        A10, A11, A12, A13, A14, A15, A16, A17, A18, A19,
        A20, A21, A22, A23, A24, A25, A26, A27, A28, A29,
        A30, A31, A32, A33, A34, A35, A36, A37, A38, A39,
        A40, A41, A42, A43, A44, A45, A46, A47, A48, A49,
        A50, A51, A52, A53, A54, A55, A56, A57, A58, A59,
        A60, A61, A62, A63, A64, A65, A66, A67, A68, A69,
        A70, A71, A72, A73, A74, A75 }

    public static void main(String[] args) {
        EnumSet<Big> bigEnumSet = EnumSet.allOf(Big.class);
        System.out.println(bigEnumSet);
    }
}
```

输出为：

```shell
[A0, A1, A2, A3, A4, A5, A6, A7, A8, A9, A10, A11, A12, A13, A14, A15, A16, A17, A18, A19, A20, A21, A22, A23, A24, A25, A26, A27, A28, A29, A30, A31, A32, A33, A34, A35, A36, A37, A38, A39, A40, A41, A42, A43, A44, A45, A46, A47, A48, A49, A50, A51, A52, A53, A54, A55, A56, A57, A58, A59, A60, A61, A62, A63, A64, A65, A66, A67, A68, A69, A70, A71, A72, A73, A74, A75]
```

显然，`EnumSet` 可以应用于超过 64 个元素的 `enum`，所以猜测，Enum 会在必要的时候增加一个 long。

### 使用 EnumMap

`EnumMap` 是一种特殊的 `Map`，它要求其中的键（key）必须来自一个 `enum`，由于 `enum` 本身的限制，所以 `EnumMap` 在内部可由数组实现。因此 `EnumMap` 的速度很快，我们可以放心地使用 `enum` 实例在 `EnumMap` 中进行查找操作。不过，我们只能将 `enum` 的实例作为键来调用 `put()` 方法，其它操作与使用与一般的 `Map` 差不多。

下面的例子演示了命令设计模式的用法。一般来说，命令模式首先需要一个只有单一方法的接口，然后从该接口实现具有各自不同的行为的多个子类。接下来，程序员就可以构造命令对象，并在需要的时候使用它们了：

```java
public class EnumMaps {
    interface Command {
        void action();
    }
    public static void main(String[] args) {
        EnumMap<AlarmPoints, Command> em = new EnumMap<>(AlarmPoints.class);
        em.put(KITCHEN,
                () -> System.out.println("Kitchen fire!"));
        em.put(BATHROOM,
                () -> System.out.println("Bathroom alert!"));
        for(Map.Entry<AlarmPoints,Command> e:
                em.entrySet()) {
            System.out.print(e.getKey() + ": ");
            e.getValue().action();
        }
        try { // If there's no value for a particular key:
            em.get(UTILITY).action();
        } catch(Exception e) {
            System.out.println("Expected: " + e);
        }
    }
}
```

输出为：

```shell
BATHROOM: Bathroom alert!
KITCHEN: Kitchen fire!
Expected: java.lang.NullPointerException
```

与 `EnumSet` 一样，`enum` 实例定义时的次序决定了其在 `EnumMap` 中的顺序。

`main()` 方法的最后部分说明，`enum` 的每个实例作为一个键，总是存在的。但是，如果你没有为这个键调用 `put()` 方法来存入相应的值的话，其对应的值就是 `null`。

## 六、枚举的应用

### switch语句中使用枚举

在`switch`中使用`enum`，是`enum`提供的一项非常便利的功能。JDK 7之后，`switch`已经支持 `int`、`char`、`String`、`enum`类型的参数，枚举通过`ordinal()`方法取得次序（编译器帮我们做了类似的工作）。

一般情况下我们必须使用 `enum` 类型来修饰一个 `enum` 实例，但是在 `case` 语句中却不必如此。下面的例子是使用 `enum` 构造了一个小型状态机：

```java
public class TrafficLight { // 红绿灯
    enum Signal {GREEN, YELLOW, RED}

    Signal color = Signal.RED;
    public void change() {
        switch (color) {
            // Note you don't have to say Signal.RED
            // in the case statement:
            case RED: color = Signal.GREEN;
                break;
            case GREEN: color = Signal.YELLOW;
                break;
            case YELLOW: color = Signal.RED;
                break;
        }
    }
    @Override
    public String toString() {
        // 输出交通灯颜色
        return "The traffic light is " + color;
    }

    public static void main(String[] args) {
        TrafficLight t = new TrafficLight();
        for (int i = 0; i < 7; i++) {
            System.out.println(t);
            t.change();
        }
    }
}
```

输出为：

```java
The traffic light is RED
The traffic light is GREEN
The traffic light is YELLOW
The traffic light is RED
The traffic light is GREEN
The traffic light is YELLOW
The traffic light is RED
```

编译器并没有报错 `switch` 中没有 `default` 语句，但并不是因为每一个 `Signal` 都有对应的 `case` 语句。如果注释掉其中的某个 `case` 语句，编译器同样不会报错。这意味着，你必须确保覆盖了所有分支。

### 使用接口组织枚举

在一个接口的内部，创建实现该接口的枚举，以此将元素进行分组，可以达到将枚举元素分类组织的目的。举例来说，假设你想用 `enum` 来表示不同类别的食物，同时还希望每个 `enum` 元素仍然保持 `Food` 类型。那可以这样实现：

```java
public interface Food {
    enum Appetizer implements Food {
        SALAD, SOUP, SPRING_ROLLS;
    }
    enum MainCourse implements Food {
        LASAGNE, BURRITO, PAD_THAI,
        LENTILS, HUMMOUS, VINDALOO;
    }
    enum Dessert implements Food {
        TIRAMISU, GELATO, BLACK_FOREST_CAKE,
        FRUIT, CREME_CARAMEL;
    }
    enum Coffee implements Food {
        BLACK_COFFEE, DECAF_COFFEE, ESPRESSO,
        LATTE, CAPPUCCINO, TEA, HERB_TEA;
    }
}
```

对于 enum 而言，实现接口是使其子类化的唯一办法，所以嵌入在 Food 中的每个 enum 都实现了 Food 接口。现在，在下面的程序中，我们可以说“所有东西都是某种类型的 Food"。

```java
public class TypeOfFood {
    public static void main(String[] args) {
        Food food = Food.Appetizer.SALAD;
        food = Food.MainCourse.LASAGNE;
        food = Food.Dessert.GELATO;
        food = Food.Coffee.CAPPUCCINO;
    }
}
```

如果 enum 类型实现了 Food 接口，那么我们就可以将其实例向上转型为 Food，所以上例中的所有东西都是 Food。

然而，当你需要与一大堆类型打交道时，接口就不如 enum 好用了。例如，如果你想创建一个“枚举的枚举”，那么可以创建一个新的 enum，然后用其实例包装 Food 中的每一个 enum 类：

```java
public enum  Course {
    APPETIZER(Food.Appetizer.class),
    MAINCOURSE(Food.MainCourse.class),
    DESSERT(Food.Dessert.class),
    COFFEE(Food.Coffee.class);
    private Food[] values;
    private Course(Class<? extends Food> kind) {
        values = kind.getEnumConstants();
    }
    public Food randomSelection() {
        return Enums.random(values);
    }
}
public class Enums {
    private static Random rand = new Random(47);
    public static <T extends Enum<T>> T random(Class<T> ec) {
        return random(ec.getEnumConstants());
    }
    public static <T> T random(T[] values) {
        return values[rand.nextInt(values.length)];
    }
}
```

每一个 Course 的实例都将其对应的 Class 对象作为构造器的参数。通过 getEnumConstants0 方法，可以从该 Class 对象中取得某个 Food 子类的所有 enum 实例。这些实例在 randomSelection() 中被用到。因此，通过从每一个 Course 实例中随机地选择一个 Food，我们便能够生成一份菜单：

```java
public class Meal {
    public static void main(String[] args) {
        for(int i = 0; i < 5; i++) {
            for(Course course : Course.values()) {
                Food food = course.randomSelection();
                System.out.println(food);
            }
            System.out.println("***");
        }
    }
}
```

输出为：

```shell
SPRING_ROLLS
VINDALOO
FRUIT
DECAF_COFFEE
***
SOUP
VINDALOO
FRUIT
TEA
***
SALAD
BURRITO
FRUIT
TEA
***
SALAD
BURRITO
CREME_CARAMEL
LATTE
***
SOUP
BURRITO
TIRAMISU
ESPRESSO
***
```

在这个例子中，我们通过遍历每一个 `Course` 实例来获得 "枚举的枚举" 的值。

此外，还有一种更简洁的管理枚举的办法，就是将一个 `enum` 嵌套在另一个 `enum` 内，例如：

```java
public enum SecurityCategory {
    STOCK(Security.Stock.class),
    BOND(Security.Bond.class);
    Security[] values;
    SecurityCategory(Class<? extends Security> kind) {
        values = kind.getEnumConstants();
    }

    interface Security {
        enum Stock implements Security {
            SHORT, LONG, MARGIN
        }
        enum Bond implements Security {
            MUNICIPAL, JUNK
        }
    }
    public Security randomSelection() {
        return Enums.random(values);
    }

    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            SecurityCategory category = Enums.random(SecurityCategory.class);
            System.out.println(category + ": " +
                    category.randomSelection());
        }
    }
}
```

输出为：

```shell
BOND: MUNICIPAL
BOND: MUNICIPAL
STOCK: MARGIN
STOCK: MARGIN
BOND: JUNK
STOCK: SHORT
STOCK: LONG
STOCK: LONG
BOND: MUNICIPAL
BOND: JUNK
```

`Security` 接口的作用是将其所包含的 `enum` 组合成一个公共类型，这一点是有必要的。然后，`SecurityCategory` 才能将 `Security` 中的 `enum` 作为其构造器的参数使用，以起到组织的效果。

如果我们将这种方式应用于 `Food` 的例子，结果应该这样：

```java
public enum Meal2 {
    APPETIZER(Food.Appetizer.class),
    MAINCOURSE(Food.MainCourse.class),
    DESSERT(Food.Dessert.class),
    COFFEE(Food.Coffee.class);
    private Food[] values;
    private Meal2(Class<? extends Food> kind) {
        values = kind.getEnumConstants();
    }
    public interface Food {
        enum Appetizer implements Food {
            SALAD, SOUP, SPRING_ROLLS;
        }
        enum MainCourse implements Food {
            LASAGNE, BURRITO, PAD_THAI,
            LENTILS, HUMMOUS, VINDALOO;
        }
        enum Dessert implements Food {
            TIRAMISU, GELATO, BLACK_FOREST_CAKE,
            FRUIT, CREME_CARAMEL;
        }
        enum Coffee implements Food {
            BLACK_COFFEE, DECAF_COFFEE, ESPRESSO,
            LATTE, CAPPUCCINO, TEA, HERB_TEA;
        }
    }
    public Food randomSelection() {
        return Enums.random(values);
    }
    public static void main(String[] args) {
        for(int i = 0; i < 5; i++) {
            for(Meal2 meal : Meal2.values()) {
                Food food = meal.randomSelection();
                System.out.println(food);
            }
            System.out.println("***");
        }
    }
}
```

输出为：

```shell
SPRING_ROLLS
VINDALOO
FRUIT
DECAF_COFFEE
***
SOUP
VINDALOO
FRUIT
TEA
***
SALAD
BURRITO
FRUIT
TEA
***
SALAD
BURRITO
CREME_CARAMEL
LATTE
***
SOUP
BURRITO
TIRAMISU
ESPRESSO
***
```

其实，这仅仅是重新组织了一下代码，不过多数情况下，这种方式使你的代码具有更清晰的结构。

### 策略枚举

EffectiveJava中展示了一种策略枚举。这种枚举通过枚举嵌套枚举的方式，将枚举常量分类处理。

这种做法虽然没有switch语句简洁，但是更加安全、灵活。

```java
public enum PayrollDay {
    MONDAY(PayType.WEEKDAY), TUESDAY(PayType.WEEKDAY), WEDNESDAY(
            PayType.WEEKDAY), THURSDAY(PayType.WEEKDAY), FRIDAY(PayType.WEEKDAY), SATURDAY(
            PayType.WEEKEND), SUNDAY(PayType.WEEKEND);

    private final PayType payType;

    PayrollDay(PayType payType) {
        this.payType = payType;
    }

    double pay(double hoursWorked, double payRate) {
        return payType.pay(hoursWorked, payRate);
    }

    // 策略枚举
    private enum PayType {
        WEEKDAY {
            double overtimePay(double hours, double payRate) {
                return hours <= HOURS_PER_SHIFT ? 0 : (hours - HOURS_PER_SHIFT)
                        * payRate / 2;
            }
        },
        WEEKEND {
            double overtimePay(double hours, double payRate) {
                return hours * payRate / 2;
            }
        };
        private static final int HOURS_PER_SHIFT = 8;

        abstract double overtimePay(double hrs, double payRate);

        double pay(double hoursWorked, double payRate) {
            double basePay = hoursWorked * payRate;
            return basePay + overtimePay(hoursWorked, payRate);
        }
    }

    public static void main(String[] args) {
        System.out.println("时薪100的人在周五工作8小时的收入：" + PayrollDay.FRIDAY.pay(8.0, 100));
        System.out.println("时薪100的人在周六工作8小时的收入：" + PayrollDay.SATURDAY.pay(8.0, 100));
    }
}
```

输出为：

```shell
时薪100的人在周五工作8小时的收入：800.0
时薪100的人在周六工作8小时的收入：1200.0
```

### 常量特定方法

Java中允许为 `enum` 的实例允许编写方法，从而为每个 `enum` 实例赋予各自不同的行为。要实现常量相关的方法，你需要为 `enum` 定义一个或多个 `abstract` 方法，然后为每个 `enum` 实例实现该抽象方法。例如：

```java
public enum ConstantSpecificMethod {
    DATE_TIME {
        @Override
        String getInfo() {
            return DateFormat.getDateInstance().format(new Date());
        }
    },
    CLASSPATH {
        @Override
        String getInfo() {
            return System.getenv("CLASSPATH");
        }
    },
    VERSION {
        @Override
        String getInfo() {
            return System.getProperty("java.version");
        }
    };
    abstract String getInfo();
    public static void main(String[] args) {
        for (ConstantSpecificMethod csm : values()) {
            System.out.println(csm.getInfo());
        }
    }
}
```

输出为：

```shell
2020年3月29日
null
11
```

通过相应的 `enum` 实例，我们可以调用其上的方法。这通常也称为表驱动的代码（table-driven code）。

在面向对象的程序设计中，不同的行为与不同的类关联。而通过常量相关的方法，每个 `enum` 实例可以具备自己独特的行为，这似乎说明每个 `enum` 实例就像一个独特的类。在上面的例子中，`enum` 实例似乎被当作其“超类” `ConstantSpecificMethod` 来使用，在调用 `getInfo()` 方法时，体现出多态的行为。

再看一个更有趣的关于洗车的例子。每个顾客在洗车时，都有一个选择菜单，每个选择对应一个不同的动作。可以将一个常量相关的方法关联到一个选择上，再使用一个 `EnumSet` 来保存客户的选择：

```java
public class CarWash {
    public enum Cycle {
        UNDERBODY { // 底部
            @Override
            void action() { // 喷涂底部
                System.out.println("Spraying the underbody");
            }
        },
        WHEELWASH { // 轴距
            @Override
            void action() { // 清洗车轮
                System.out.println("Washing the wheels");
            }
        },
        PREWASH {   // 预洗
            @Override
            void action() { // 污渍
                System.out.println("Loosening the dirt");
            }
        },
        BASIC { // 基本
            @Override
            void action() { // 最基本的清洗
                System.out.println("The basic wash");
            }
        },
        HOTWAX {    // 热蜡
            @Override
            void action() { // 应用热蜡
                System.out.println("Applying hot wax");
            }
        },
        RINSE { //漂洗
            @Override
            void action() { // 冲刷
                System.out.println("Rinsing");
            }
        },
        BLOWDRY {   // 吹干
            @Override
            void action() { // 吹干
                System.out.println("Blowing dry");
            }
        };
        abstract void action();
    }
    EnumSet<Cycle> cycles =
            EnumSet.of(Cycle.BASIC, Cycle.RINSE);
    public void add(Cycle cycle) {
        cycles.add(cycle);
    }
    public void washCar() {
        for(Cycle c : cycles)
            c.action();
    }
    @Override
    public String toString() {
        return cycles.toString();
    }
    public static void main(String[] args) {
        CarWash wash = new CarWash();
        System.out.println(wash);
        wash.washCar();
// Order of addition is unimportant:
        wash.add(Cycle.BLOWDRY);
        wash.add(Cycle.BLOWDRY); // Duplicates ignored
        wash.add(Cycle.RINSE);
        wash.add(Cycle.HOTWAX);
        System.out.println(wash);
        wash.washCar();
    }
}
```

输出为：

```shell
[BASIC, RINSE]
The basic wash
Rinsing
[BASIC, HOTWAX, RINSE, BLOWDRY]
The basic wash
Applying hot wax
Rinsing
Blowing dry
```

与使用匿名内部类相比，定义常量相关方法的语法更高效，简洁。

除了实现 `abstract` 方法以外，也可以覆盖实现的常量相关的方法，例如：

```java
public enum  OverrideConstantSpecific {
    NUT, BOLT,
    WASHER {
        @Override
        void f() {
            System.out.println("Overridden method");
        }
    };
    void f() {
        System.out.println("default behavior");
    }
    public static void main(String[] args) {
        for(OverrideConstantSpecific ocs : values()) {
            System.out.print(ocs + ": ");
            ocs.f();
        }
    }
}
```

输出为：

```shell
NUT: default behavior
BOLT: default behavior
WASHER: Overridden method
```