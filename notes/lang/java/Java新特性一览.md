十一、特性

Java 各版本的新特性

New highlights in Java SE 1.8  

1. Lambda Expressions
2. Pipelines and Streams
3. Date and Time API
4. Default Methods
5. Type Annotations
6. Nashhorn JavaScript Engine
7. Concurrent Accumulators
8. Parallel operations
9. PermGen Error Removed

New highlights in Java SE 1.7  

1. Strings in Switch Statement
2. Type Inference for Generic Instance Creation
3. Multiple Exception Handling
4. Support for Dynamic Languages
5. Try with Resources
6. Java nio Package
7. Binary Literals, Underscore in literals
8. Diamond Syntax

- Difference between Java 1.8 and Java 1.7?
- Java 8 特性

New highlights in Java SE 1.6

1. Web服务元数据
2. 脚本语言支持
3. JTable的排序和过滤
4. 更简单，更强大的JAX-WS
5. 轻量级Http Server
6. 嵌入式数据库Derby

New highlights in Java SE 1.5

1. 泛型（Generic）
2. 类型安全枚举
3. 内省（Introspector）
4. 静态导入
5. 可变长参数（Varargs）
6. 遍历循环（foreach循环）
7. 自动装箱与拆箱
8. 元数据（动态注解）

New highlights in Java SE 1.4

1. 正则表达式
2. 异常链
3. NIO
4. 日志类
5. XML解析器
6. XSLT转换器

New highlights in Java SE 1.3

1. 类库改进（数学运算、Timer API等）
2. JNDI服务作为平台级服务提供
3. 引入Java Sound API

New highlights in Java SE 1.2

1. 引入集合框架
2. 对字符串常量做内存映射
3. 引入JIT（Just In Time）编译器
4. 引入对打包的Java文件进行数字签名
5. 引入控制授权访问系统资源的策略工具
6. 引入JFC（Java Foundation Classes），包括Swing 1.0, 拖放和Java2D类库
7. 引入Java插件
8. 在JDBC中引入可滚动结果集，BLOB，CLOB，批量更新和用户自定义类型
9. 在Applet中添加声音支持

New highlights in Java SE 1.1

1. 引入JDBC（Java DataBase Connectivity）
2. 支持内部类
3. 引入Java Bean
4. 引入RMI（Remote Method Invocation）
5. 引入反射（仅用于内省）

New highlights in Java SE 1.0

1. Java虚拟机
2. Applet
3. AWT

Java 与 C++ 的区别

- Java 是纯粹的面向对象语言，所有的对象都继承自 java.lang.Object，C++ 为了兼容 C 即支持面向对象也支持面向过程。
- Java 通过虚拟机从而实现跨平台特性，但是 C++ 依赖于特定的平台。
- Java 没有指针，它的引用可以理解为安全指针，而 C++ 具有和 C 一样的指针。
- Java 支持自动垃圾回收，而 C++ 需要手动回收。
- Java 不支持多重继承，只能通过实现多个接口来达到相同目的，而 C++ 支持多重继承。
- Java 不支持操作符重载，虽然可以对两个 String 对象执行加法运算，但是这是语言内置支持的操作，不属于操作符重载，而 C++ 可以。
- Java 的 goto 是保留字，但是不可用，C++ 可以使用 goto。

What are the main differences between Java and C++?

JRE or JDK

- JRE is the JVM program, Java application need to run on JRE.
- JDK is a superset of JRE, JRE + tools for developing java programs. e.g, it provides the compiler "javac"
