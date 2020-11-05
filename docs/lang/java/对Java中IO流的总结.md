# 概述

Java I/O用于处理输入输出。

Java中IO流分为：

- 按照流，可以分为输入流和输出流；
- 按照操作单元划分，可以分为字节流和字符流；
- 按照流的角色划分，可以分为节点流和处理流。

字节流和字符流操作的本质区别只有一个：字节流是原生的操作，字符流是经过处理后的操作。

字节流在操作时不会用到缓冲区，也就是不会用到内存，文件本身直接操作，而字符流在操作时使用了缓冲区，通过缓冲区操作文件。

Java IO中包含了许多InputStream、OutputStream、Reader、Writer的子类。这样设计的原因是让每一个类都负责不同的功能。这也就是为什么IO包中有这么多不同的类的缘故。它的用途主要有：文件访问、网络访问、内存缓存访问、线程内部通信（管道）、缓冲、过滤、解析、读写文本（Readers/Writers）、读写基本类型数据（long、int）、读写对象。


# 字节流

字节流是继承自输入字节流`InputStream`与输出字节流`OutputStream`，它们都是抽象类，是字节流的父类。

`InputStream`常见子类有：

`FileInputStream`从文件中读取信息
`ByteArrayInputStream`字节数组输入流
`ObjectInputStream`序列化时使用一般和ObjectOutputStream一起使用
`FilterInputStream`过滤输入流，为基础的输入流提供一些额外的操作。

# 字符流

`Reader`类是Java IO中所有`Reader`的基类。`Writer`类是Java IO中所有`Writer`的基类。