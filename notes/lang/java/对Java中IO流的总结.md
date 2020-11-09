# 概述

Java I/O用于处理输入输出。

Java中IO流分为：

- 按照流，可以分为输入流和输出流；
- 按照操作单元划分，可以分为字节流和字符流；
- 按照流的角色划分，可以分为节点流和处理流。

字节流和字符流操作的本质区别只有一个：字节流是原生的操作，字符流是经过处理后的操作。

字节流在操作时不会用到缓冲区，也就是不会用到内存，文件本身直接操作，而字符流在操作时使用了缓冲区，通过缓冲区操作文件。

Java IO中包含了许多InputStream、OutputStream、Reader、Writer的子类。这样设计的原因是让每一个类都负责不同的功能。这也就是为什么IO包中有这么多不同的类的缘故。它的用途主要有：文件访问、网络访问、内存缓存访问、线程内部通信（管道）、缓冲、过滤、解析、读写文本（Readers/Writers）、读写基本类型数据（long、int）、读写对象。

# 输入/输出流

在 Java API 中，可以从其中读入一个字节序列的对象称作输入流，而可以向其中写入一个字节序列的对象称作输出流。这些字节序列的来源地和目的地可以是文件，而且通常都是文件，但是也可以是网络连接，甚至是内存块。抽象类 InputStream 和 OutputStream 构成了输入/输出（I/O）类层次结构的基础。

因为面向字节的流不便于处理以 Unicode 形式存储的信息，所以从抽象类 Reader 和 Writer 中继承出来了一个专门用于处理 Unicode 字符的单独的类层次结构。这些类拥有的读入和写出操作都是基于两字节的 Char 值的（即，Unicode 码元），而不是基于 byte 值的。

# 字节流

字节流是继承自输入字节流`InputStream`与输出字节流`OutputStream`，它们都是抽象类，是字节流的父类。

`InputStream`常见子类有：

`FileInputStream`从文件中读取信息
`ByteArrayInputStream`字节数组输入流
`ObjectInputStream`序列化时使用一般和ObjectOutputStream一起使用
`FilterInputStream`过滤输入流，为基础的输入流提供一些额外的操作。

# 字符流

`Reader`类是Java IO中所有`Reader`的基类。`Writer`类是Java IO中所有`Writer`的基类。