能够分析类能力的程序称为反射，而 Java 中的反射库提供了可以在程序运行时，动态地操纵 Java 中代码。反射机制是极其强大的一种机制通过反射机制可以：

- 在运行时分析类的能力。
- 在运行时查看对象。
- 在运行时任意调用对象的方法。

反射是一种功能强大且复杂的机制。下面就来了解 Java 中的反射。

## Class

Java 中的 `Class` 类是用来维护程序运行时的类型标识，它包含了与类有关的信息，这个类跟踪着每个对象所属的类。虚拟机利用运行时类型信息选择相应的方法执行。

在 Java 中，一切皆对象，因此创建的实例都是 `Object` 的子类，而 `Object` 提供了 `getClass()` 方法返回一个 `Class` 类型的对象。

```java
String s = "一切皆对象";
Class c = s.getClass();
```

除了上述使用使用实例对象的`getClass()`方法获取 `Class` 对象外，还有：

- `Class.forName(String className)`    使用类的完全限定名来获取类对象。如 `Class.forName("java.lang.String")`。
- `ClassName.class`：使用类的字面常量的方式获取类对象。如 `String.class`。

这里需要注意，`Class.forName()` 找不到要加载的类时，会抛出 `ClassNotFoundException` 异常。

而 `ClassName.class` 方式来获取 `Class` 对象被称为类字面常量。这样做不仅简单，而且更安全，因为它在编译时会受到检查，并且它根除了对 `Class.forName()` 方法的调用，所以效率更高。

类字面常量不仅可以用于普通类，也可以用于接口、数组以及基本数据类型。另外，对于基本数据类型的包装类，还有一个静态域 `TYPE`，用于指向对应的基本数据类型的 `Class` 对象。如下代码所示：

```java
Boolean.class;    // class java.lang.Boolean
Boolean.TYPE;    // boolean
boolean.class;    // boolean
```

如下表格给出了基本数据类型与包装类型对应的关系。

|基本数据类型|包装类|
|:---:|:---:|
|boolean.class|Boolean.TYPE|
|char.class|Character.TYPE|
|byte.class|Byte.TYPE|
|short.class|Short.TYPE|
|int.class|Integer.TYPE|
|long.class|Long.TYPE|
|float.class|Float.TYPE|
|double.class|Double.TYPE|
|void.class|Void.TYPE|

`Class` 类引入后，我们可以通过它指向对象的确切类型。但是没有约束的 `Class` 引用可以随意赋值，如下所示：

```java
Class c = Integer.class;
c = String.class;
```

但当 Java SE 5 加入了泛型后，`Class` 类如下所示：

```java
public final class Class<T> implements java.io.Serializable,
                              GenericDeclaration,
                              Type,
                              AnnotatedElement
```

这样可以对 `Class` 引用所指向的 `Class` 对象的类型进行限定，即使由于泛型会在运行期间被擦除，但是编译期足以确保我们使用正确的对象类型。如下所示：

```java
Class<Integer> c = Integer.class;
c = String.class;    // Error: 不兼容的类型
```

这里使用泛型限定的类引用会使得编译器将强制进行类型检查，报错 `java.lang.Class<java.lang.String>`  无法转换为 `java.lang.Class<java.lang.Integer>`。

但这里可以使用 `?` 通配符来解决无法转换的问题：

```java
Class<?> c = Integer.class;
c = String.class;
```

这里也可以使用通配符 `extends` 关键字来限定 `Class` 范围，如下所示：

```java
 Class<? extends Number> c = Integer.class;
c = Double.class;
c = String.class;    // Error: 不兼容的类型
```

## 反射API

Java 已经在 `java.lang.reflect` 包中提供了 `Field`、`Method` 和 `Constructor` 三个类来分别描述类的域、方法和构造器。而关于一个类的描述是用 `Class` 类来描述的。因此，想要获取关于一个类的 `Field`、`Method` 和 `Constructor`，就需要通过 `Class` 类提供的方法获取。

`Class` 中的 `getFields`、`getMethods` 和 `getConstructors` 方法将分别返回类提供的 `public` 域、方法和构造器数组，其中包括超类的公有成员。`Class` 类的 `getDeclareFields`、`getDeclareMethods` 和 `getDeclaredConstructors` 方法贾昂分别返回类中声明的全部域、方法和构造器，其中包括私有和受保护成员，但不包括超类的成员。

### Field

`Field`提供有关类或接口的域的信息，以及对它的动态访问权限。反射的域可能是一个静态域或实例域。可以通过 `Class` 类提供的方法获取。如下所示：

- `Field getDeclaredField(String name)`    返回指定 `name` 名称的域。包含被 `private` 修饰的，但不包含继承的域。
- `Field[] getDeclaredFields()`    返回 `Class` 对象所表示的类或接口中的所有域。包含被 `private` 修饰的，但不包含继承的域。
- `Field getField(String name)`    返回指定`name`名称的域。只能返回被 `public` 修饰的域，但包含继承的域。
- `Field[] getFields()`    返回 `Class` 对象所表示的类中的所有域。只能返回被 `public` 修饰的域，但包含继承的域。

### Method

`Method`提供关于类或接口上单独某个方法，以及如何访问该方法的信息，所反映的方法可能是类方法或实例方法，也包括抽象方法。可以通过 `Class` 类提供的方法获取。如下所示：

- `Method getDeclaredMethod(String name, Class<?>... parameterTypes)`    获取当前类的某个方法，但不包含继承的方法。
- `Method[] getDeclaredMethods()`    获取当前类的所有方法，但不包含继承的方法。
- `Method getMethod(String name, Class<?>... parameterTypes)`    返回指定 `name` 名称的方法，包含继承的方法，但必须被 `public` 修饰的方法。
- `Method[] getMethods()`    返回所有被 `public` 修饰的方法，包含继承的方法。

### Constructor

`Constructor`类反映的是 `Class` 对象所表示的类的构造方法。可以通过 `Class` 类来获取 `Constructor`。如下所示：

- `Constructor<T> getConstructor(Class<?>... parameterTypes)`    返回指定参数类型、具有`public`访问权限的构造函数对象
- `Constructor<?>[] getConstructors()`    返回所有具有`public`访问权限的构造函数的`Constructor`对象数组
- `Constructor<T> getDeclaredConstructor(Class<?>... parameterTypes)`    返回指定参数类型、所有声明的构造函数对象，当然，也包括被 `private` 修饰的构造函数。
- `Constructor<?>[] getDeclaredConstructors()`    返回所有声明的构造函数对象，也包含被 `private` 修饰的构造函数。
- `T newInstance()`：创建此`Class` 对象所表示的类的一个新实例。

### AccessibleObject

当通过 `Field` 类使用 `get` 方法查看私有的对象域，或者 `Method` 类使用 `invoke` 调用私有方法，或者是 `Constructor` 类调用非 `public` 构造器，都会受限于 Java 的访问控制。如果没有收到安全管理器的控制，就可以覆盖访问控制。因此，`Field`、`Method` 或 `Constructor` 三个类的公共的超类 `AccessibleObject` 类提供了 `setAccessible` 方法。 

### Array

`java.lang.reflect` 包中提供了 `Array` 类用来允许动态地创建和访问数组。如编写一个数组复制的方法，就可以使用 `Array.newInstance()` 方法创建一个指定类型的新数组。

```java
Object newArray = Array.newInstance(componentType, newLength);
```

这里 `componentType`  表示数组中元素的类型，`newLength` 表示数组的长度。下面就实现一个创建复制数组的方法：

```java
public static Object copyOf(Object a, int newLength) {
    Class<?> cl = a.getClass();
    if (!cl.isArray()) return null;
    Class<?> componentType = cl.getComponentType();
    int length = Array.getLength(a);
    Object newArray = Array.newInstance(componentType, newLength);
    System.arraycopy(a, 0, newArray, 0, Math.min(length, newLength));
    return newArray;
}
```

这里使用 `Object` 而不是对象数组，是因为如整型数组类型 `int[]` 可以被转换成 `Object`，但不能转换成对象数组。

`java.lang.reflect.Array` 还提供了 `get` 和 `set` 方法用来设置和获取指定位置的元素等有用的方法。

## 总结

通过使用反射，可以了解任意一个类中的所有域和方法，并且调用它们。这样可以提高代码的灵活性和复用率。但是反射虽好，也不能滥用，因为反射用不好会影响程序的性能。下面几条内容应该牢记于心。

- **性能开销**    反射涉及了动态类型的解析，所以 JVM 无法对这些代码进行优化。因此，反射操作的效率要比那些非反射操作低得多。应该避免在经常被执行的代码或对性能要求很高的程序中使用反射。
- **使用反射会模糊程序内部逻辑**    程序员希望在源代码中看到程序的逻辑，反射等绕过了源代码的技术，因而会带来维护问题。反射代码比相应的直接代码更复杂。
- **安全限制**    使用反射技术要求程序必须在一个没有安全限制的环境中运行。
- **内部暴露**    由于反射允许代码执行一些在正常情况下不被允许的操作，比如访问私有的属性和方法，所以使用反射可能会导致意料之外的副作用，这可能会使代码功能失调并破坏可移植性。反射代码破坏了抽象性，因此当平台发生改变的时候，代码的行为就有可能也随着变化。
