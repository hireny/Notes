# 十、反射

每个类都有一个   **Class**   对象，包含了与类有关的信息。当编译一个新类时，会产生一个同名的 .class 文件，该文件内容保存着 Class 对象。

类加载相当于 Class 对象的加载，类在第一次使用时才动态加载到 JVM 中。也可以使用 `Class.forName("com.mysql.jdbc.Driver")` 这种方式来控制类的加载，该方法会返回一个 Class 对象。

反射可以提供运行时的类信息，并且这个类可以在运行时才加载进来，甚至在编译时期该类的 .class 不存在也可以加载进来。

Class 和 java.lang.reflect 一起对反射提供了支持，java.lang.reflect 类库主要包含了以下三个类：

-   **Field**  ：可以使用 get() 和 set() 方法读取和修改 Field 对象关联的字段；
-   **Method**  ：可以使用 invoke() 方法调用与 Method 对象关联的方法；
-   **Constructor**  ：可以用 Constructor 的 newInstance() 创建新的对象。

**反射的优点：**  

*     **可扩展性**   ：应用程序可以利用全限定名创建可扩展对象的实例，来使用来自外部的用户自定义类。
*     **类浏览器和可视化开发环境**   ：一个类浏览器需要可以枚举类的成员。可视化开发环境（如 IDE）可以从利用反射中可用的类型信息中受益，以帮助程序员编写正确的代码。
*     **调试器和测试工具**   ： 调试器需要能够检查一个类里的私有成员。测试工具可以利用反射来自动地调用类里定义的可被发现的 API 定义，以确保一组测试中有较高的代码覆盖率。

**反射的缺点：**  

尽管反射非常强大，但也不能滥用。如果一个功能可以不用反射完成，那么最好就不用。在我们使用反射技术时，下面几条内容应该牢记于心。

*     **性能开销**   ：反射涉及了动态类型的解析，所以 JVM 无法对这些代码进行优化。因此，反射操作的效率要比那些非反射操作低得多。我们应该避免在经常被执行的代码或对性能要求很高的程序中使用反射。

*     **安全限制**   ：使用反射技术要求程序必须在一个没有安全限制的环境中运行。如果一个程序必须在有安全限制的环境中运行，如 Applet，那么这就是个问题了。

*     **内部暴露**   ：由于反射允许代码执行一些在正常情况下不被允许的操作（比如访问私有的属性和方法），所以使用反射可能会导致意料之外的副作用，这可能导致代码功能失调并破坏可移植性。反射代码破坏了抽象性，因此当平台发生改变的时候，代码的行为就有可能也随着变化。

## 反射的API

### 关于Field字段的API

- `Field getField(String name)`：根据字段名获取某个public的field（包括父类）
- `Field getDeclaredField(String name)`：根据字段名获取当前类的某个field（不包括父类）
- `Field[] getFields()`：获取所有public的field（包括父类）
- `Field[] getDeclaredFields()`：获取当前类的所有field（不包括父类）

### 关于Method方法的API

- `Method getMethod(String name, Class... args)`：获取某个public的Method（包括父类）
- `Method getDeclaredMethod(String name, Class... args)`：获取当前类的某个Method（不包括父类）
- `Method[] getMethods()`：获取所有public的Method（包括父类）
- `Method[] getDeclaredMethods()`：获取当前类的所有Method（不包括父类）

一个 `Method` 对象包含一个方法的所有信息：

- `getName()` ：返回方法名称，例如："getScore"
- `getReturnType()`：返回方法返回值类型，也是一个Class实例，例如：`String.class`
- `getParameterTypes()`：返回方法的参数类型，是一个Class数组，例如：`{String.class, int.class}`
- `getModifiers()`：返回方法的修饰符，它是一个 `int`，不同的bit表示不同的含义

### 关于Constructor构造器的API

- `getConstructor(Class... args)`：获取某个 public 的 Constructor
- `getDeclaredConstructor(Class... args)`：获取某个Constructor
- `getConstructors()`：获取所有public的Constructor
- `getDeclaredConstructors()`：获取所有Constructor

> 注意：`Constructor` 总是当前类定义的构造方法，和父类无关，因此不存在多态的问题。
> 调用非 `public` 的 `Constructor` 时，必须首先通过 `setAccessible(true)` 设置允许访问。`setAccessible(true)` 可能会失败。

`Constructor` 对象封装了构造方法的所有信息；

通过 `Class` 实例的方法可以获取 `Constructor` 实例：`getConstructor()`，`getConstructors()`，`getDeclaredConstructor()`，`getDeclaredConstructors()`;

通过 `Construcotr` 实例可以创建一个实例对象：`newInstance(Object... parameters)`；通过设置 `setAccessible(true)` 来访问非 `public` 构造方法。


- [Trail: The Reflection API](https://docs.oracle.com/javase/tutorial/reflect/index.html)
- [深入解析 Java 反射（1）- 基础](http://www.sczyh30.com/posts/Java/java-reflection-1/)