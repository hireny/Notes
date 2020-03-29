# 注解

- 基本语法
- 元注解
- 使用注解


注解（也被称为元数据）是 Java 5 引入的语言变化之一，它是附加在代码中的一些元信息，用于一些工具在编译、运行时进行解析和使用，起到说明、配置的功能。注解不会也不能影响代码的实际逻辑，仅仅起到辅助性的作用。并拥有如下优势：简单已读的代码，编译器类型检查，使用 `annotation API`为自己的注解构造处理工具。即使 Java 定义了一些类型的元数据，但是一般来说注解类型的添加和如何使用完全取决于你。

注解的语言十分简单，主要是在现有语法中添加 @ 符号。Java 5 引入了前三种定义在 `java.lang` 包中的注解：

- `@Override`：表示当前的方法定义将覆盖基类的方法。如果你不小心拼写错误，或者方法签名被错误拼写的时候，编译器就会发出错误提示。
- `@Deprecated`：如果使用该注解的元素被调用，编译器就会发出警告信息。
- `@SuppressWarnings`：关闭不当的编译器警告信息。
- `@SafeVarargs`：在 Java 7 中加入用于禁止对具有泛型 `varargs` 参数的方法或构造函数的调用方发出警告。
- `@FunctionalInterface`：Java 8 中加入用于表示类型声明为函数式接口

还有 5 中额外的注解类型用于创造新的注解。

## 基本语法

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

### 元注解

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

## 编写注解处理器

Java注解本身对代码逻辑没有任何影响。因此使用注解就要创建与使用注解处理器。我们使用反射机制的 API 来编写这类工具。同时还提供 `javac` 编译器钩子在编译时使用注解。

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

### 注解元素

在 `UseCase.java` 中定义的 `@UseCase` 的标签包含 int 元素 `id` 和 `String` 元素 `description`。注解元素可用的类型如下所示：

- 所有基本类型（int、float、boolean等）
- String
- Class
- enum
- Annotation
- 以上类型的数组

如果你使用了其它类型，编译器会报错。注意，也不允许使用任何包装类型，但是由于自动装箱的存在，这不算是什么限制。注解也可以作为元素的类型（注解嵌套是一个非常有用的技巧）。

### 默认值限制

编译器对于元素的默认值有些过于挑剔。首先，元素不能有不确定的值。也就是说，元素要么有默认值，要门就在使用注解时提供元素的值。

这里有另外一个限制：任何非基本类型的元素，无论是在源代码声明时还是在注解接口中定义默认值时，都不能使用 `null` 作为其值。这个限制使得处理器很难表现一个元素的存在或者缺失的状态，因为在每个注解的声明中，所有的元素都存在，并且具有相应的值。为了绕开这个约束，可以自定义一些特殊的值，比如空字符串或者负数用于表达某个元素不存在。

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface SimulatingNull {
    int id() default -1;
    String description() default "";
}
```

这是一个在定义注解的习惯用法。

### 生成外部文件

当有些框架需要一些额外的信息才能与你的源代码协同工作，这种情况下注解就会变得十分有用。像 Enterprise JavaBeans (EJB3 之前)这样的技术，每一个 Bean 都需要需要大量的接口和部署描述文件，而这些就是“样板”文件。Web Service，自定义标签库以及对象/关系映射工具（例如 Toplink 和 Hibernate）通常都需要 XML 描述文件，而这些文件脱离于代码之外。除了定义 Java 类，程序员还必须忍受沉闷，重复的提供某些信息，例如类名和包名等已经在原始类中已经提供的信息。每当你使用外部描述文件时，他就拥有了一个类的两个独立信息源，这经常导致代码的同步问题。同时这也要求了为项目工作的程序员在知道如何编写 Java 程序的同时，也必须知道如何编辑描述文件。

假设你想提供一些基本的对象/关系映射功能，能够自动生成数据库表。你可以使用 XML 描述文件来指明类的名字、每个成员以及数据库映射的相关信息。但是，通过使用注解，你可以把所有信息都保存在 JavaBean 源文件中。为此你需要一些用于定义数据库名称、数据库列以及将 SQL 类型映射到属性的注解。

以下是一个注解的定义，它告诉注解处理器应该创建一个数据库表：

```java
@Target(ElementType.TYPE) // Applies to classes only
@Retention(RetentionPolicy.RUNTIME)
public @interface DBTable {
    String name() default "";
}
```

在 `@Target` 注解中指定的每一个 `ElementType` 就是一个约束，它告诉编译器，这个自定义的注解只能用于指定的类型。你可以指定 `enum ElementType` 中的一个值，或者以逗号分割的形式指定多个值。如果想要将注解应用于所有的 `ElementType`，那么可以省去 `@Target` 注解，但是这并不常见。

注意 `@DBTable` 中有一个 `name()` 元素，该注解通过这个元素为处理器创建数据库时提供表的名字。

如下是修饰字段的注解：

```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Constraints {
    boolean primaryKey() default false;
    boolean allowNull() default true;
    boolean unique() default false;
}
```

```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface SQLString {
    int value() default 0;
    String name() default "";
    Constraints constraints() default @Constraints;
}
```

```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface SQLInteger {
    String name() default "";
    Constraints constraints() default @Constraints;
}
```

`@Constraints` 注解允许处理器提供数据库表的元数据。`@Constraints` 代表了数据库通常提供的约束的一小部分，但是它所要表达的思想已经很清楚了。`primaryKey()`，`allowNull()` 和 `unique()` 元素明显的提供了默认值，从而使得咋大多数情况下，该注解的使用者不需要输入太多东西。

另外两个 `@interface` 定义的是 SQL 类型。如果希望这个框架更有价值的话，我们应该为每个 SQL 类型都定义相应的注解。不过为为示例，两个元素足够了。

这些 SQL 类型具有 `name()` 元素和 `constraints()` 元素。后者利用了嵌套注解的功能，将数据库列的类型约束信息嵌入其中。注意 `constraints()` 元素的默认值是 `@Constraints`。由于在 `@Constraints` 注解类型之后，没有在括号中指明 `@Constraints` 元素的值，因此，`constraints()` 的默认值为所有元素都为默认值的 `@Constraints` 注解。如果要使得嵌入的 `@Constraints` 注解中的 `unique()` 元素为 true，并作为 `constraints()` 元素的默认值，你可以像如下定义：

```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Uniqueness {
    Constraints constraints()
            default @Constraints(unique = true);
}
```

下面是一个简单的，使用了如上注解的类：

```java
@DBTable(name = "MEMBER")
public class Member {
    @SQLString(30)
    String firstName;
    @SQLString(50)
    String lastName;
    @SQLInteger
    Integer age;
    @SQLString(value = 30, constraints = @Constraints(primaryKey = true))
    String reference;
    static int memerCount;
    public String getFirstName() {
        return firstName;
    }
    public String getLastName() {
        return lastName;
    }
    public Integer getAge() {
        return age;
    }
    public String getReference() {
        return reference;
    }
    public static int getMemerCount() {
        return memerCount;
    }
    @Override
    public String toString() {
        return reference;
    }
}
```

类注解 `@DBTable` 注解给定了元素值 `MEMBER`，它将会作为标的名字。类的属性 `firstName` 和 `lastName` 都被注解为 `@SQLString` 类型并且给了默认元素值分别为 `30` 和 `50`。这些注解都有两个有趣的地方：首先，他们都使用了嵌入的 `@Constraints` 注解的默认值；其次，它们都是用了快捷方式特性。如果你在注解中定义了名为 `value` 的元素，并且在使用该注解时，`value` 为唯一一个需要赋值的元素，你就不需要使用名—值对的语法，你只需要在括号中给出 `value` 元素的值即可。这可以应用于任何合法类型的元素。这也限制了你必须将元素命名为 `value`，不过在上面的例子中，这样的注解语句也更易于理解：

```java
@SQLString(30)
```

处理器将在创建表的时候使用该值设置 SQL 列的大小。

默认值的语法虽然很灵巧，但是它很快就变的复杂起来。以 `reference` 字段的注解为例，上面拥有 `@SQLString` 注解，但是这个字段也将成为表的主键，因此在嵌入的 `@Constraint` 注解中设定 `primaryKey` 元素的值。这时事情就变得复杂了。你不得不为这个嵌入的注解使用很长的键-值对的形式，来指定元素名称和 `@interface` 的名称。同时，由于有特殊命名的 `value` 也不是唯一需要赋值的元素，因此不能再使用快捷方式特性。如你所见，最终结果不算清晰易懂。

### 替代方案

可以使用多种不同的方式来定义自己的注解用于上述任务。例如，你可以使用一个单一的注解类 `@TableColumn`，它拥有一个 `enum` 元素，元素值定义了 `STRING`，`INTEGER`，`FLOAT` 等类型。这消除了每个 SQL 类型都需要定义一个 `@interface` 的负担，不过也使得用额外信息修饰 SQL 类型变的不可能，这些额外的信息例如长度或精度等，都可能是非常有用的。

你也可以使用一个 `String` 类型的元素来描述实际的 SQL 类型，比如 `“VARCHAR(30)”` 或者 `“INTEGER”`。这使得你可以修饰 SQL 类型，但是这也将 Java 类型到 SQL 类型的映射绑在了一起，这不是一个好的设计。你并不想在数据库更改之后重新编译你的代码；如果我们只需要告诉注解处理器，我们正在使用的是什么“口味（favor）”的 SQL，然后注解助力器来为我们处理 SQL 类型的细节，那将是一个优雅的设计。

第三种可行的方案是一起使用两个注解，`@Constraints` 和相应的 SQL 类型（例如，`@SQLInteger`）去注解同一个字段。这可能会让代码有些混乱，但是编译器允许你对同一个目标使用多个注解。在 Java 8，在使用多个注解的时候，你可以重复使用同一个注解。


### 注解不支持继承

你不能使用 `extends` 关键字来继承 `@interface`。这真是一个遗憾，如果可以定义 `@TableColumn` 注解（参考前面的建议），同时嵌套一个 `@SQLType` 类型的注解，将成为一个优雅的设计。按照这种方式，你可以通过继承 `@SQLType` 来创造各种 SQL 类型。例如 `@SQLInteger` 和 `@SQLString`。如果支持继承，就会大大减少打字的工作量并且使得语法更整洁。在 Java 的未来版本中，似乎没有任何关于让注解支持继承的提案，所以在当前情况下，上例中的解决方案可能已经是最佳方案了。

### 实现处理器

下面是一个注解处理器的例子，它将读取一个类文件，检查上面的数据库注解，并生成用于创建数据库的SQL命令：

```java
public class TableCreator {
    public static void main(String[] args) throws ClassNotFoundException {
        if (args.length < 1) {
            System.out.println(
                    "arguments: annotated classes");
            System.exit(0);
        }
        for (String className : args) {
            Class<?> cl = Class.forName(className);
            DBTable dbTable = cl.getAnnotation(DBTable.class);
            if (dbTable == null) {
                System.out.println(
                        "No DBTable annotations in class " +
                                className);
                continue;
            }
            String tableName = dbTable.name();
            // If the name is empty, use the Class name:
            if (tableName.length() < 1)
                tableName = cl.getName().toUpperCase();
            List<String> columnDefs = new ArrayList<>();
            for (Field field : cl.getDeclaredFields()) {
                String columnName = null;
                Annotation[] anns =
                        field.getDeclaredAnnotations();
                if (anns.length < 1)
                    continue; // Not a db table column
                if (anns[0] instanceof SQLInteger) {
                    SQLInteger sInt = (SQLInteger) anns[0];
                    // Use field name if name not specified
                    if (sInt.name().length() < 1)
                        columnName = field.getName().toUpperCase();
                    else
                        columnName = sInt.name();
                    columnDefs.add(columnName + " INT" +
                            getConstraints(sInt.constraints()));
                }
                if (anns[0] instanceof SQLString) {
                    SQLString sString = (SQLString) anns[0];
                    // Use field name if name not specified.
                    if (sString.name().length() < 1)
                        columnName = field.getName().toUpperCase();
                    else
                        columnName = sString.name();
                    columnDefs.add(columnName + " VARCHAR(" +
                            sString.value() + ")" +
                            getConstraints(sString.constraints()));
                }
                StringBuilder createCommand = new StringBuilder(
                        "CREATE TABLE " + tableName + "(");
                for (String columnDef : columnDefs)
                    createCommand.append(
                            "\n " + columnDef + ",");
                // Remove trailing comma
                String tableCreate = createCommand.substring(
                        0, createCommand.length() - 1) + ");";
                System.out.println("Table Creation SQL for " +
                        className + " is:\n" + tableCreate);
            }
        }
    }
    private static String getConstraints(Constraints con) {
        String constraints = "";
        if (!con.allowNull())
            constraints += " NOT NULL";
        if (con.primaryKey())
            constraints += " PRIMARY KEY";
        if (con.unique())
            constraints += " UNIQUE";
        return constraints;
    }
}
```

输出为：

```sql
Table Creation SQL for me.hireny.tutorial.java.java5.annotation.db.Member is:
CREATE TABLE MEMBER(
 FIRSTNAME VARCHAR(30));
Table Creation SQL for me.hireny.tutorial.java.java5.annotation.db.Member is:
CREATE TABLE MEMBER(
 FIRSTNAME VARCHAR(30),
 LASTNAME VARCHAR(50));
Table Creation SQL for me.hireny.tutorial.java.java5.annotation.db.Member is:
CREATE TABLE MEMBER(
 FIRSTNAME VARCHAR(30),
 LASTNAME VARCHAR(50),
 AGE INT);
Table Creation SQL for me.hireny.tutorial.java.java5.annotation.db.Member is:
CREATE TABLE MEMBER(
 FIRSTNAME VARCHAR(30),
 LASTNAME VARCHAR(50),
 AGE INT,
 REFERENCE VARCHAR(30) PRIMARY KEY);
```

主方法会循环处理命令行传入的每一个类名。每一个类都是用 `forName()` 方法进行加载，并使用 `getAnnotation(DBTable.class)` 来检查该类是否带有 `@DBTable` 注解。如果存在，将表名存储起来。然后读取这个类的所有字段，并使用 `getDeclaredAnnotations()` 进行检查。这个方法返回一个包含特定字段上所有注解的数组。然后使用 `instanceof` 操作符判断这些注解是否是 `@SQLInteger` 或者 `@SQLString` 类型。如果是的话，在对应的处理快中将构造出相应的数据库列的字符串片段。注意，由于注解没有继承机制，如果要获取近似多态的行为，使用 `getDeclaredAnnotations()` 似乎是唯一的方式。

嵌套的 `@Constraint` 注解被传递给 `getConstraints()` 方法，并用它来构造一个包含 SQL 约束的 `String` 对象。

需要提醒的是，上面演示的技巧对于真实的对象/映射关系而言，是十分幼稚的。使用 `@DBTable` 的注解来获取表的名称，这使得如果要修改表的名字，则迫使你重新编译 Java 代码。这种效果并不理想。现在已经有了很多可用的框架，用于将对象映射到数据库中，并且越来越多的框架开始使用注解了。

## 使用 javac 处理注解

通过 `javac`，你可以通过创建编译时（compile-time）注解处理器在 Java 源文件上使用注解，而不是编译之后的  class 文件。但是这里有一个重大限制：你不能通过处理器来改变源代码。唯一影响输出的方式就是创建新的文件。

如果你的注解处理器创建了新的源文件，在新一轮处理中注解会检查源文件本身。工具在检测一轮之后持续循环，直到不再有新的源文件产生。然后它编译所有的源文件。

每一个你编写的注解都需要处理器，但是 `javac` 可以非常容易的将多个注解处理器合并在一起。你可以指定多个需要处理的类，并且你可以添加监听器用于监听注解处理完成后接到通知。

本节中的示例将帮助你开始学习，但如果你必须深入学习，请做好反复学习，大量访问 Google 和StackOverflow 的准备。

### 最简单的处理器

让我们开始定义我们能想到的最简单的处理器，只是为了编译和测试。如下是注解的定义：

```java
@Retention(RetentionPolicy.SOURCE)
@Target({ElementType.TYPE,ElementType.METHOD,
        ElementType.CONSTRUCTOR,
        ElementType.ANNOTATION_TYPE,
        ElementType.PACKAGE, ElementType.FIELD,
        ElementType.LOCAL_VARIABLE})
public @interface Simple {
    String value() default "-default-";
}
```

`@Retention` 的参数现在为 `SOURCE`，这意味着注解不会再存留在编译后的代码。这在编译时处理注解是没有必要的，它只是指出，在这里，`javac` 是唯一有机会处理注解的代码。

`@Target` 声明了几乎所有的目标类型（除了`PACKAGE`），同样是为了演示。下面是一个测试示例。

```java
@Simple
public class SimpleTest {
    @Simple
    int i;
    @Simple
    public SimpleTest() {}
    @Simple
    public void foo() {
        System.out.println("SimpleTest.foo()");
    }
    @Simple
    public void bar(String s, int i, float f) {
        System.out.println("SimpleTest.bar()");
    }
    @Simple
    public static void main(String[] args) {
        @Simple
        SimpleTest st = new SimpleTest();
        st.foo();
    }
}
```

输出为：

```java
SimpleTest.foo()
```

在这里我们使用 `@Simple` 注解了所有 `@Target` 声明允许的地方。

`SimpleTest.java` 只需要 `Simple.java` 就可以编译成功。当我们编译的时候什么都没有发生。

`javac` 允许 `@Simple` 注解（只要它存在）在我们创建处理器并将其 `hook` 到编译器之前，不做任何事情。

如下是一个十分简单的处理器，其所作的事情就是把注解相关的信息打印出来：


如果只是通过平常的方式编译 `SimpleTest.java`，你不会得到任何结果。为了得到注解输出，你必须增加一个 `processor` 标志并且连接注解处理器类

```shell
C:\Users\hireny\IdeaProjects\java-tutorial\src\main\java>javac me/hireny/tutorial/java/java5/annotation/simplest/Simple.java

C:\Users\hireny\IdeaProjects\java-tutorial\src\main\java>javac me/hireny/tutorial/java/java5/annotation/simplest/SimpleTest.java

C:\Users\hireny\IdeaProjects\java-tutorial\src\main\java>javac me/hireny/tutorial/java/java5/annotation/simplest/SimpleProcessor.java

C:\Users\hireny\IdeaProjects\java-tutorial\src\main\java>javac -processor me.hireny.tutorial.java.java5.annotation.simplest.SimpleProcessor
me/hireny/tutorial/java/java5/annotation/simplest/SimpleTest.java
```

现在编译器有了输出

```shell
me.hireny.tutorial.java.java5.annotation.simplest.Simple
==== me.hireny.tutorial.java.java5.annotation.simplest.SimpleTest ====
CLASS : [public] : SimpleTest : me.hireny.tutorial.java.java5.annotation.simplest.SimpleTest
me.hireny.tutorial.java.java5.annotation.simplest.SimpleTest
java.lang.Object
i,SimpleTest(),foo(),bar(java.lang.String,int,float),main(java.lang.String[])
==== i ====
FIELD : [] : i : int
==== SimpleTest() ====
CONSTRUCTOR : [public] : <init> : ()void
==== foo() ====
METHOD : [public] : foo : ()void
void foo()
==== bar(java.lang.String,int,float) ====
METHOD : [public] : bar : (java.lang.String,int,float)void
void bar(s,i,f)
==== main(java.lang.String[]) ====
METHOD : [public, static] : main : (java.lang.String[])void
void main(args)
```

这给了你一些可以发现的东西，包括参数名和类型、返回值等。

### 更复杂的处理器

当你创建用于 `javac` 注解处理器时，你不能使用 Java 的反射特性，因为你处理的是源代码，而并非是编译后的 `class` 文件。各种 mirror[^3] 解决这个问题的方法是，通过允许你在未编译的源代码中查看方法、字段和类型。

如下是一个用于提取类中方法的注解，所以它可以被抽取成为一个接口：

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.SOURCE)
public @interface ExtractInterface {
    String interfaceName() default "-!!-";
}
```

`RetentionPolicy` 的值为 `SOURCE`，这是为了在提取类中的接口之后不再将注解信息保留在 `class` 文件中。接下来的测试类提供了一些公共方法，这些方法可以成为接口的一部分：

```java
@ExtractInterface(interfaceName = "IMultiplier")
public class Multiplier {
    public boolean flag = false;
    private int n = 0;
    public int multiply(int x, int y) {
        int total = 0;
        for (int i = 0; i < x; i++) {
            total = add(total, y);
        }
        return total;
    }

    public int fortySeven() {
        return 47;
    }
    private int add(int x, int y) {
        return x + y;
    }
    public double timesTen(double arg) {
        return arg * 10;
    }

    public static void main(String[] args) {
        Multiplier m = new Multiplier();
        System.out.println("11 * 16 = " + m.multiply(11, 16));
    }
}
```

输出为：

```shell
11 * 16 = 176
```

`Multiplier` 类（只能处理正整数）拥有一个 `multiply() ` 方法，这个方法会多次调用私有方法 `add()` 来模拟乘法操作。`add()` 是私有方法，因此不能成为接口的一部分。其他的方法提供了语法多样性。注解被赋予 `IMultiplier` 的 `InterfaceName` 作为要创建的接口的名称。

这里有一个编译时处理器用于提取有趣的方法，并创建一个新的 interface 源代码文件（这个源文件将会在下一轮中被自动编译）：

```java
@SupportedAnnotationTypes(
        "annotations.ifx.ExtractInterface")
@SupportedSourceVersion(SourceVersion.RELEASE_8)
public class IfaceExtractorProcessor
        extends AbstractProcessor {
    private ArrayList<Element>
            interfaceMethods = new ArrayList<>();
    Elements elementUtils;
    private ProcessingEnvironment processingEnv;
    @Override
    public void init(
            ProcessingEnvironment processingEnv) {
        this.processingEnv = processingEnv;
        elementUtils = processingEnv.getElementUtils();
    }
    @Override
    public boolean process(
            Set<? extends TypeElement> annotations,
            RoundEnvironment env) {
        for(Element elem:env.getElementsAnnotatedWith(
                ExtractInterface.class)) {
            String interfaceName = elem.getAnnotation(
                    ExtractInterface.class).interfaceName();
            for(Element enclosed :
                    elem.getEnclosedElements()) {
                if(enclosed.getKind()
                        .equals(ElementKind.METHOD) &&
                        enclosed.getModifiers()
                                .contains(Modifier.PUBLIC) &&
                        !enclosed.getModifiers()
                                .contains(Modifier.STATIC)) {
                    interfaceMethods.add(enclosed);
                }
            }
            if(interfaceMethods.size() > 0)
                writeInterfaceFile(interfaceName);
        }
        return false;
    }
    private void
    writeInterfaceFile(String interfaceName) {
        try(
                Writer writer = processingEnv.getFiler()
                        .createSourceFile(interfaceName)
                        .openWriter()
        ) {
            String packageName = elementUtils
                    .getPackageOf(interfaceMethods
                            .get(0)).toString();
            writer.write(
                    "package " + packageName + ";\n");
            writer.write("public interface " +
                    interfaceName + " {\n");
            for(Element elem : interfaceMethods) {
                ExecutableElement method =
                        (ExecutableElement)elem;
                String signature = " public ";
                signature += method.getReturnType() + " ";
                signature += method.getSimpleName();
                signature += createArgList(
                        method.getParameters());
                System.out.println(signature);
                writer.write(signature + ";\n");
            }
            writer.write("}");
        } catch(Exception e) {
            throw new RuntimeException(e);
        }
    }
    private String createArgList(
            List<? extends VariableElement> parameters) {
        String args = parameters.stream()
                .map(p -> p.asType() + " " + p.getSimpleName())
                .collect(Collectors.joining(", "));
        return "(" + args + ")";
    }
}
```

Elements 对象实例 elementUtils 是一组静态方法的工具；我们用它来寻找 writeInterfaceFile() 中含有的包名。

getEnclosedElements()方法会通过指定的元素生成所有的“闭包”元素。在这里，这个类闭包了它的所有元素。通过使用 getKind() 我们会找到所有的 public 和 static 方法，并将其添加到 interfaceMethods 列表中。接下来 writeInterfaceFile() 使用 interfaceMethods 列表里面的值生成新的接口定义。注意，在 writeInterfaceFile() 使用了向下转型到 ExecutableElement，这使得我们可以获取所有的方法信息。createArgList() 是一个帮助方法，用于生成参数列表。

Filer是 getFiler() 生成的，并且是 PrintWriter 的一种实例，可以用于创建新文件。我们使用 Filer 对象，而不是原生的 PrintWriter 原因是，这个对象可以运行 javac 追踪你创建的新文件，这使得它可以在新一轮中检查新文件中的注解并编译文件。

如下是一个命令行，可以在编译的时候使用处理器：

```java
javac -processor annotations.ifx.IfaceExtractorProcessor Multiplier.java
```

新生成的 IMultiplier.java 的文件，正如你通过查看上面处理器的 println() 语句所猜测的那样，如下所示：

```java
package annotations.ifx;
public interface IMultiplier {
    public int multiply(int x, int y);
    public int fortySeven();
    public double timesTen(double arg);
}
```

这个类哦同样会被 `javac` 编译（在某一轮中），所以你会在同一个目录中看到 `IMultiplier.class` 文件。

## 基于注解的单元测试

单元测试是对类中每个方法提供一个或者多个测试的一种事件，其目的是为了有规律的测试一个类中每个部分是否具备正确的行为。在 Java 中，最著名的单元测试工具就是 JUnit。JUnit 4 版本已经包含了注解。在注解版本之前的 JUnit 一个最主要的问题是，为了启动和运行 JUnit 测试，有大量的“仪式”需要标注。这种负担已经减轻了一些，但是注解使得测试更接近“可以工作的最简单的测试系统”。

在注解版本之前的 JUnit，你必须创建一个单独的文件来保存单元测试。通过注解，我们可以将单元测试集成在需要被测试的类中，从而将单元测试的时间和麻烦降到了最低。这种方式有额外的好处，就是使得测试私有方法和公有方法变的一样容易。

这个基于注解的测试框架叫做 @Unit。其最基本的测试形式，可能也是你使用的最多的一个注解是 @Test，我们使用 @Test 来标记测试方法。测试方法不带参数，并返回 boolean 结果来说明测试方法成功或者失败。你可以任意命名它的测试方法。同时 @Unit 测试方法可以是任意你喜欢的访问修饰方法，包括 private。

要使用 @Unit，你必须导入 onjava.atunit 包，并且使用 @Unit 的测试标记为合适的方法和字段打上标签（在接下来的例子中你会学到），然后让你的构建系统对编译后的类运行 @Unit，下面是一个简单的例子：

```java
public class AtUnitExample1 {
    public String methodOne() {
        return "This is methodOne";
    }
    public int methodTwo() {
        System.out.println("This is methodTwo");
        return 2;
    }
    @Test
    boolean methodOneTest() {
        return methodOne().equals("This is methodOne");
    }
    @Test
    boolean m2() { return methodTwo() == 2; }
    @Test
    private boolean m3() { return true; }
    // Shows output for failure:
    @Test
    boolean failureTest() { return false; }
    @Test
    boolean anotherDisappointment() {
        return false;
    }
}
```

编译上面的代码：

```shell
PS C:\Users\hireny\IdeaProjects\java-tutorial\src\main\java> javac .\onjava\atunit\AtUnit.java
PS C:\Users\hireny\IdeaProjects\java-tutorial\src\main\java> javac .\me\hireny\tutorial\java\java5\annotation\AtUnitExample1.java
PS C:\Users\hireny\IdeaProjects\java-tutorial\src\main\java> java onjava.atunit.AtUnit me/hireny/tutorial/java/java5/annotation/AtUnitExample1.class
```

输出为：

```shell
me.hireny.tutorial.java.java5.annotation.AtUnitExample1
  . anotherDisappointment (failed)
  . m2 This is methodTwo

  . methodOneTest
  . m3
  . failureTest (failed)
(5 tests)

>>> 2 FAILURES <<<
  me.hireny.tutorial.java.java5.annotation.AtUnitExample1: anotherDisappointment
  me.hireny.tutorial.java.java5.annotation.AtUnitExample1: failureTest
```

使用 `@Unit` 进行测试的类必须定义在某个包中（即必须包括`package`声明）。

@Test 注解被置于 methodOneTest()、 m2()、m3()、failureTest() 以及 anotherDisappointment() 方法之前，它们告诉 @Unit 方法作为单元测试来运行。同时 @Test 确保这些方法没有任何参数并且返回值为 boolean 或者 void。当你填写单元测试时，唯一需要做的就是决定测试是成功还是失败，（对于返回值为 boolean 的方法）应该返回 ture 还是 false。

如果你熟悉 JUnit，你还将注意到 @Unit 输出的信息更多。你会看到现在正在运行的测试的输出更有用，最后它会告诉你导致失败的类和测试。

你并非必须将测试方法嵌入到原来的类中，有时候这种事情根本做不到。要生产一个非嵌入式的测试，最简单的方式就是继承：

```java
public class AUExternalTest extends AtUnitExample1 {
    @Test
    boolean _MethodOne() {
        return methodOne().equals("This is methodOne");
    }
    @Test
    boolean _MethodTwo() {
        return methodTwo() == 2;
    }
}
```

编译上述代码：

```shell
PS C:\Users\hireny\IdeaProjects\java-tutorial\src\main\java> javac .\me\hireny\tutorial\java\java5\annotation\AUExternalTest.java
PS C:\Users\hireny\IdeaProjects\java-tutorial\src\main\java> java onjava.atunit.AtUnit .\me\hireny\tutorial\java\java5\annotation\AUExternalTest.class
```
输出为：

```shell
me.hireny.tutorial.java.java5.annotation.AUExternalTest
  . _MethodTwo This is methodTwo

  . _MethodOne
OK (2 tests)
```

这个实力还表现出灵活命名的价值。在这里，`@Test` 方法被命名为下划线前缀加上要测试的方法名称（我并不认为这是一种理想的命名形式，这只是表现一种可能性罢了）。

你也可以使用组合来创建非嵌入式的测试：

```java
```