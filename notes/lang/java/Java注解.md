- 使用注解
  - 注解简介  373
  - 一个示例：注解事件处理器 374
- 注解语法 379
  - 注解接口 379
  - 注解  380
  - 注解各类声明 382
  - 注解类型用法 383
  - 注解 this 384
- 标准注解 385
  - 用于编译的注解 386
  - 用于管理资源的注解 386
  - 元注解 387 
- 源码级注解处理 389
  - 注解处理 389
  - 语言模型API 390
  - 使用注解来生成源码 390

主机是那些插入到源代码中使用其他工具可以对其进行处理的标签。这些工具可以在源码层次上进行操作，或者可以处理编译器在其中放置了注解的类文件。

主机不会改变程序的编译方式。Java 编译器对于包含主机和不包含注解的代码会生成相同的虚拟机指令。

为了能够受益于注解，你需要选择一个处理工具，然后向你的处理工具可以理解的代码中插入注解，之后运用该处理工具处理代码。

注解的使用范围还是很广泛的，并且这种广泛性让人乍一看会觉得有些杂乱无章。下面是关于注解的一些可能的用法：

- 附属文件的自动生成，例如部署描述符或者 bean 信息类。
- 测试、日志、事务语义等代码的自动生成。

```java
public class MyClass {
    ...
    @Test
    public void checkRandomInsertion()
}
```

注解 @Test 用于注解 checkRandomInsertions 方法。

在 Java 中，注解是当作一个修饰符来使用的，它被置于被注解项之前，中间没有分号。每一个注解的名称前面都加上了 @ 符号，这有点类似于 Javadoc 的注释。然而，Javadoc 注释出现在 /***/ 定界符的内部，而注解是代码的一部分。

@Test 注解自身并不会做任何事情，它需要工具支持才会有用。例如，当测试一个类的时候，JUnit4测试工具可能会调用所有标识为 @Test 的方法。另一个工具可能会删除一个类文件中的所有测试方法，以便在对这个类测试完毕后，不会将这些测试方法与程序装载在一起。

注解可以定义成包含元素的形式，例如：

@Test(timeout="10000")

这些元素可以被读取这些注解的工具去处理。其他形式的元素也是由可能的；

除了方法外，还可以注解类、成员以及局部变量，这些注解可以存在于任何可以设置一个像 public 或者 static 这样的修饰符的地方。另外，你还可以注解包、参数变量、类型参数和类型用法。

每个注解都必须通过一个注解接口进行定义。这些接口中的方法与注解中的元素相对应。例如，JUnit 的注解 Test 可以用下面这个接口进行定义：

@Target(ElementType.METHOD)

@Retention(RetentionPolicy.RUNTIME)

public @interface Test {

​    long timeout() default 0L;

}

@interface 声明创建了一个真正的 Java 接口。处理注解的工具将接收那些实现了这个注解接口的对象。这类工具可以调用 timeout 方法来检索某个特定 Test 注解的 timeout 元素。

注解 Target 和 Retention 是元注解。它们注解了 Test 注解，即将 Test 注解标识成一个只能运用到方法上的注解，并且当类文件载入到虚拟机的时候，仍可以保留下来。

## 注解语法

了解注解语法

### 注解接口

注解是由注解接口来定义的：

```java
modifiers @interface AnnotationName {
    elementDeclaration1
    elementDeclaration2
}
```

每个元素声明都具有下面这种形式：

```java
type elementName();
```

或者

```java
type elementName() default value;
```

--缓存--

Java 注解是 Java SE 5.0 版本开始支持加入源代码的特殊语法元数据。

Java语言中的类、方法、变量、参数和包等都可以被标注。和 Javadoc 不同，Java标注可以通过反射获取标注内容。在编译器生成类文件时，标注可以被嵌入到字节码中，Java虚拟机可以保留标注内容，在运行时可以获取到标注内容。当然它也支持自定义Java标注。

从 JDK 1.5 开始，Java开始支持注解。AnnotationProcessing tool 提供了一个补充性的编译时注解操作的接口。文档 JSR-269 将它规范化，在 JDK 1.6 写入编译器 javac 中。

Java 定义了一套注解，共有 7 个，3 个在 java.lang 中，剩下 4 个在 java.lang.annotation 中。

作用在代码的注解是：

- @Override - 检查该方法是否是重写方法。如果发现其父类，或者是引用的接口中并没有该方法时，会报编译错误。
- @Deprecated - 标记过时方法。如果使用该方法，会报编译警告。
- @SuppressWarnings - 指示编译器去忽略注解中声明的警告。

作用在其他注解的注解（或者是元注解）是：

- @Retention - 表示这个注解怎么保存，是只在代码中，还是编入 class 文件中，或者是在运行时可以通过反射访问。
- @Documented - 标记这些注解是否包含在用户文档中。
- @Target - 标记这个注解应该是哪种 Java 成员。
- @Inherited - 标记这个注解是继承于哪个注解类（默认 注解并没有继承于任何子类）

从 Java 7 开始，额外添加 3 个注解：

- @SafeVarargs - Java 7 开始支持，忽略任何使用参数为泛型变量的方法或构造函数调用产生的警告。
- @FunctionInterface - Java 8 开始支持，标识一个匿名函数或函数式接口。
- @Repeatable - Java 8 开始支持，标识某注解可以在同一个声明上使用多次。

# 注解

- 基本语法
- 元注解
- 内置注解
- 注解处理


注解（也被称为元数据）是 Java 5 引入的语言变化之一，它是附加在代码中的一些元信息，用于一些工具在编译、运行时进行解析和使用，起到说明、配置的功能。

注解可以被用在包、类、方法、变量和参数上，注解不会影响到代码的逻辑，只起到辅助的作用。

注解的语言十分简单，主要是在现有语法中添加 @ 符号。

## 基本语法

### 定义注解

Java语言使用 `@interface` 语法来定义注解（`Annotation`），它的格式如下：

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Test {
}
```

注解的定义也需要一些元注解（meta-annotation），比如 `@Target` 和 `@Retention`。`@Target` 定义你的注解应用的位置（方法还是字段）。`Retention` 定义注解的生命周期，在源代码中（SOURCE）、class文件（CLASS）中或者是在运行时（RUNTIME）。

不包含任何元素的注解称为标记注解（marker annotation），例如 `@Test`。

注解通常会包含一些表示特定值的元素。当分析处理注解的时候，程序或工具可以利用这些值。注解的元素看起来就像接口的方法，但是可以为其指定默认值。

下面是一个简单的注解，我们可以用它来追踪项目中的用例。程序员可以使用该注解来标注满足特定用例的一个方法或者一组方法。于是，项目经理可以通过统计已经实现的用例来掌控项目的进展，而开发者在维护项目时可以轻松的找到用例用于更新，或者他们可以调试系统中业务逻辑。

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface UseCase {
    int id();
    String description() default "no description";
}
```

注意 id 和 description 与方法定义类似。由于编译器会对 id 进行类型检查，因此将跟踪数据库与用例文档和源代码相关联是可靠的方式。description 元素拥有一个 default 值，如果在注解某个方法时没有给出 description 的值。则该注解的处理器会使用此元素的默认值。

在下面的类中，有三个方法被注解为用例：

```java
public class PasswordUtils {
    @UseCase(id = 47, description =
            "Passwords must contain at least one numeric")
    public boolean validatePassword(String passwd) {
        return (passwd.matches("\\w*\\d\\w*"));
    }
    @UseCase(id = 48)
    public String encryptPassword(String passwd) {
        return new StringBuilder(passwd)
                .reverse().toString();
    }
    @UseCase(id = 49, description =
            "New passwords can't equal previously used ones")
    public boolean checkForNewPassword(
            List<String> prevPasswords, String passwd) {
        return !prevPasswords.contains(passwd);
    }
}
```

注解的元素在使用时表现为 名-值 对的形式，并且需要放置在 `@UseCase` 声明之后的括号内。在 `encryptPassword()` 方法的注解中，并没有给出 `description` 的默认值，所以在 `@interface UseCase` 的注解处理器分析处理这个类的时候会使用该元素的默认值。

你应该能够想象到如何使用这套工具来“勾勒”出将要建造的系统，然后在建造的过程中逐渐实现系统的各项功能。

### 使用注解

下面的例子中，使用 `@Test` 对 `testExecute()` 进行注解。该注解本身不做任何事情。但是编译器要保证其类路径上有 `@Test` 注解的定义。

```java
public class Testable {
    public void execute() {
        System.out.println("Executing...");
    }
    @Test
    void testExecute() {
        execute();
    }
}
```

被注解标注的方法和其它方法没有任何区别。在这个例子中，注解 `@Test` 可以和任何修饰符共同用于方法，诸如 `public`、`static` 或 `void`。从语法的角度上看，注解的使用方式和修饰符的使用方式一致。

## 元注解

有一些注解可以修饰其它注解，这些注解就称为元注解（meta annotation）。Java标准库已经定义了一些元注解，我们只需要使用元注解，通常不需要自己去编写元注解。

**@Target**

使用`@Target`可以定义`Annotation`能够被应用于源码的哪些位置：

- `ElementType.CONSTRUCTOR`：构造器的声明
- `ElementTyoe.FIELD`：字段声明（包括`enum`实例）
- `ElementType.LOCAL_VARIABLE`：局部变量声明
- `ElementType.METHOD`：方法声明
- `ElementType.PACKAGE`：包声明
- `ElementType.PARAMETER`：参数声明
- `ElementType.TYPE`：类、接口（包括注解类型）或者`enum`声明

例如，定义注解`@Report`可用在方法上，我们必须添加一个`@Target(ElementType.METHOD)`：

```java
@Target(ElementType.METHOD)
public @interface Report {
	int type() default 0;
	String level() default "info";
	String value() default "";
}
```

定义注解`@Report`可用在方法上或字段上，可以把`@Target`注解参数变为数组`{ElementType.METHOD, ElementType.FIELD}`：

```java
@Target({
	ElementType.METHOD,
	ElementType.FIELD
})
public @interface Report {...}
```

实际上`@Target`定义的`value`是`ElementType[]`数组，只有一个元素时，可以省略数组的写法。

**@Retention**

使用元注解`@Retention`定义了`Annotation`的生命周期：

- `RetentionPolicy.SOURCE`：注解将被编译器丢弃（仅编译期间可用）
- `RetentionPolicy.CLASS`：注解在 class 文件中可用，但是会被 VM 丢弃
- `RetentionPolicy.RUNTIME`：VM将在运行期也保留注解，因此可以通过反射机制读取注解的信息。

如果`@Retention`不存在，则该`Annotation`默认为`CLASS`。因为通常我们自定义的`Annotation`都是`RUNTIME`，所以，务必要加上`@Retention(RetentionPolicy.RUNTIME)`这个元注解：

```java
@Retention(RetentionPolicy.RUNTIME)
public @interface Report {
	int type() default 0;
	String level() default "info";
	String value() defalut "";
}
```

**@Documented**  

使用 `@Documented` 定义的注解表明这个注解应该被 `javadoc` 工具记录，默认情况下，`javadoc` 是不包括注解的，但如果声明注解时指定了 `@Documented`，则它会被 `javadoc` 之类的工具处理，所以注解类型信息也会被包括在生成的文档中，是一个标记注解，没有成员。

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface Report {
	int type() default 0;
	String level() default "info";
	String value() default "";
}
```

**@Inherited**

使用`@Inherited`定义子类是否可继承父类定义的`Annotation`。`@Inherited`仅针对`@Target(ElementType.TYPE)`类型的`Annotation`有效，并且仅针对`class`的继承，对`interface`的继承无效：

```java
@Inherited
@Target(ElementType.TYPE)
public @interface Report {
	int type() default 0;
	String level() default "info";
	String value() default "";
}
```

在使用的时候，如果一个类用到了`@Report`

```java
@Report
public class Person {}
```

则它的子类默认也定义了该注解：

```java
public class Student extends Person {}
```

**@Repeatable**

使用元注解 `@Repeatable` 可以定义`Annotation`是否可重复（Java 8）。这个注解应用不是特别广泛。

```java
@Repeatable(Reports.class)
@Target(ElementType.TYPE)
public @interface Report {
	int type() default 0;
	String level() default "info";
	String value() default "";
}

@Target(ElementType.Type)
public @interface Reports {
	Report[] value();
}
```

经过`@Repeatable`修饰后，在某个类型声明处，就可以添加多个`@Report`注解：

```java
@Report(type=1, level="debug")
@Report(type=2, level="warning")
public class Hello {
}
```

## 内置注解

Java 5 引入了前三种定义在 `java.lang` 包中的注解：

- `@Override`：表示当前的方法定义将覆盖基类的方法。如果你不小心拼写错误，或者方法签名被错误拼写的时候，编译器就会发出错误提示。
- `@Deprecated`：如果使用该注解的元素被调用，编译器就会发出警告信息。
- `@SuppressWarnings`：关闭不当的编译器警告信息。
- `@SafeVarargs`：在 Java 7 中加入用于禁止对具有泛型 `varargs` 参数的方法或构造函数的调用方发出警告。
- `@FunctionalInterface`：Java 8 中加入用于表示类型声明为函数式接口


## 注解处理

Java注解本身对代码逻辑没有任何影响。因此使用注解就要创建与使用注解处理器。我们使用反射机制的 API 来编写这类工具。

### 获取注解

Java 反射API包含了许多方法来在运行时从类，方法或者其它元素获取注解。接口 `AnnotatedElement` 包含了大部分重要的方法，如下：

- `getAnnotations()`：返回该元素的所有注解，包括没有显式定义该元素上的注解。
- `isAnnotationPresent(Class)`：检查传入的注解是否存在于当前元素。
- `getAnnotation(Class)`：按照传入的参数获取指定类型的注解。返回`null`说明当前元素不带有此注解。

上述方法都可以被 `java.lang.Class`、`java.lang.reflect.Method`、`java.lang.reflect.Field`和`java.lang.reflect.Constructor`元素调用。

例如：

```java
// 判断`@UseCase`是否存在于`PasswordUtils`
boolean isUseCase = PasswordUtils.class.isAnnotationPresent(UseCase.class)
// 获取PasswordUtils定义的@UseCase注解
UseCase useCase = PasswordUtils.class.getAnnotation(UseCase.class);
int id = useCase.id();
String description = useCase.description();
```

使用反射API读取`Annotation`有两种方法。

方法一是先判断`Annotation`是否存在，如果存在，就直接读取：

```java
Class cls = PasswordUtils.class
if (cls.isAnnotationPresent(UseCase.class)) {
	UseCase useCase = cls.getAnnotation(UseCase.class);
	...
}
```

方法二是直接读取`Annotation`，如果`Annotation`不存在，将返回`null`：

```java
Class cls = PasswordUtils.class;
UseCase useCase = cls.getAnnotation(UseCase.class);
if (useCase != null) {
	...
}
```

读取方法、字段和构造方法的`Annotation`和Class类似。但是读取方法参数的`Annotation`就比较麻烦一点，因为方法参数本身可以看成一个数组，而每个参数又可以定义多个注解，所以，一次获取方法的所有注解就必须用一个二维数组来表示。例如，对于以下方法定义的注解：

```java
public void hello(@NotNUll @Range(max=5) String name, @NotNull String prefix) {}
```

要读取方法参数的注解，我们先用反射读取`Method`实例，然后读取方法参数的所有注解：

```java
// 获取Method实例
Method m = xxx.class.getDeclaredMethod("hello");
// 获取所有参数的 Annotation
Annotation[][] annos = m.getParameterAnnotations();
// 第一个参数（索引为0）的所有Annotation
Annotation[] annosOfName = annos[0];
for (Annotation anno : annosOfName) {
	if (anno instanceof Range) {	// @Range注解
		Range range = (Range) anno;	
	}
	if (anno instanceof NotNull) {	// @NotNull注解
		NotNull notNull = (NotNull) anno;
	}
}
```

### 注解元素

在 `UseCase.java` 中定义的 `@UseCase` 的标签包含 int 元素 `id` 和 `String` 元素 `description`。注解元素可用的类型如下所示：

简单的总结注解元素：

- 属性是以方法的形式定义的，属性名即方法名。
- 属性可用类型为：基本类型（int、float、boolean等）、String、Class、enum、Annotation和以上类型的数组。
- 可以设置默认值，那么当使用该注解的时候，不指定该属性的值则使用默认值；但设置默认值时不能使用`null`赋值，要绕开这个约束，可以自定义一些特殊的值，比如空字符串或者负数用于表达某个元素不存在。。
- 没有默认值的，使用时必须赋值。
- 若只有一种属性，且名为`value`，则赋值时可以不指定属性名
- 同样的只有一个一维数组`value[]`，则赋值时可以不指定属性名

如果你使用了其它类型，编译器会报错。注意，也不允许使用任何包装类型，但是由于自动装箱的存在，也不算什么限制。注解也可以作为元素的类型（注解嵌套是一个非常有用的技巧）。


### 注解不支持继承

你不能使用 `extends` 关键字来继承 `@interface`。但注解在编译后，编译器会自动继承 `java.lang.annotation.Annotation` 接口。


### 实现处理器

下面是一个非常简单的注解处理器，我们用来读取被注解的 `PasswordUtils` 类，并且使用反射机制来寻找 `@UseCase` 标记。给定一组 `id` 值，然后列出在 `PasswordUtils` 中找到的用例，以及缺失的用例。

```java
public class UseCaseTracker {
    public static void trackUseCases(List<Integer> useCases, Class<?> cl) {
        for (Method m : cl.getDeclaredMethods()) {
            UseCase uc = m.getAnnotation(UseCase.class);
            if (uc != null) {
                System.out.println("Found Use Case " +
                        uc.id() + "\n" + uc.description());
                useCases.remove(Integer.valueOf(uc.id()));
            }
        }
        useCases.forEach(i ->
                System.out.println("Missing use case " + i));
    }

    public static void main(String[] args) {
        List<Integer> useCases = IntStream.range(47, 51).boxed().collect(Collectors.toList());
        trackUseCases(useCases, PasswordUtils.class);
    }
}
```

输出为：

```shell
Found Use Case 49
New passwords can't equal previously used ones
Found Use Case 47
Passwords must cantain at least one numeric
Found Use Case 48
no description
Missing use case 50
```

这个程序用了两个反射的方法： `getDeclaredMethods()` 和 `getAnnotation()` ，它们都属于 `AnnotatedElement` 接口（`Class`，`Method` 与 `Field` 类都实现了该接口）。`getAnnotation()` 方法返回指定类型的注解对象，在本例中就是 "UseCase"。如果被注解的方法上没有该类型的注解，返回值就为 `null`。我们通过调用 `id()` 和 `description()` 方法来提取元素值。注意 `encryptPassword()` 方法在注解的时候没有指定 `descrpiton()` 的值，因此处理器在处理它对应的注解时，通过 `description()` 取的的是默认值 "no description"。