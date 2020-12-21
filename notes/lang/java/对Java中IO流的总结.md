Java I/O 是Java提供的一套关于输入和输出的编程接口。里面包含操作输入和输出所需要的类。

流的概念：TCP中的流指的是流入到进程或从进程流出的字节序列。

Java中IO流分为：

- 按照流，可以分为输入流和输出流；
- 按照操作单元划分，可以分为字节流和字符流；
- 按照流的角色划分，可以分为节点流和处理流。

字节流和字符流操作的本质区别只有一个：字节流是原生的操作，字符流是经过处理后的操作。

字节流在操作时不会用到缓冲区，也就是不会用到内存，文件本身直接操作，而字符流在操作时使用了缓冲区，通过缓冲区操作文件。

# 输入/输出流

## 输入流

`InputStream` 表示那些从不同数据源产生输入的类，如下所示，这些数据包括：

1. 字节数组
2. `String`对象
3. 文件
4. “管道”，从工作方式与实际生活中的管道类似：从一端输入，从另一端输出；
5. 一个由其它种类的流组成的序列，然后我们可以把它们汇聚成一个流；
6. 其它数据源，如 Internet 连接。

每种数据源都有相应的 `InputStream` 子类。另外，`FileInputStream` 也属于一种 `InputStream`，它的作用是为 “装饰器” 类提供基类。其中，“装饰器”类可以把属性或游泳的接口与输入流连接在一起。

继承 `InputStream` 的子类都有：

|类|功能|构造器参数|使用|
|:---:|:---:|:---:|:---:|
|ByteArrayInputStream||||
|FileInputStream|用于从文件中读取信息|||
|FilterInputStream|抽象类，作为“装饰器”的接口。其中，“装饰器”为其它的`InputStream`类提供有用的功能|||
|ObjectInputStream||||
|SequenceInputStream|将两个或多个`InputStream`对象转换成一个`InputStream`|||
|StringBufferInputStream|将`String`转换成`InputStream`|||
|PipedInputStream|产生用于写入相关`PipedOutStream`的数据。实现“管道化”概念|||

在 Java I/O APi 中，可以从其中读入一个字节序列的对象称作输入流，可以像其中写入一个字节序列的对象称作输出流。字节序列的来源和目的通常是文件、网络连接或内存块。输入输出流的基类主要是抽象类 `InputStream` 和 `OutputStream`。

因为面向字节的流不便于处理以 Unicode 形式存储的信息，所以从抽象类 Reader 和 Writer 中继承出来了一个专门用于处理 Unicode 字符的单独的类层次结构。这些类拥有的读入和写出操作都是基于两字节的 Char 值的（即，Unicode 码元），而不是基于 byte 值的。

`InputStream`类有一个抽象方法 `abstract int read()`，这个方法将读入一个字节，并返回读入的字节，或者在遇到输入源结尾时返回 `-1`。在设计具体的输入流类时，必须覆盖这个方法以提供适用的功能。例如，在 `FileInputStream` 中，这个方法将从某个文件中读入一个字节，而 `System.in` 却是从 “标准输入” 中读入信息，即控制台或重定向的文件。

`InputStream`类还有若干个非抽象的方法，它们可以读入一个字节数组，或者跳过大量字节。这些方法都要调用抽象的 `read` 方法，因此，各个子类都只需覆盖这一个方法。

与此类似，`OutputStream`类定义了一个抽象方法 `abstract void write(int b)`，可以像某个输出位置写出一个字节。

`read` 和 `write` 方法在执行时都将阻塞，直至字节确实被读入或写出。这意味着如果不能被立即访问，那么当前的线程将被阻塞。这使得在这两个方法等待指定的流变为可用的这段时间里，其它的线程就有机会去执行有用的工作。

`available` 方法使我们可以去检查当前可读入的字节数量，这意味着像下面的代码片段就不能被阻塞：

```java
int bytesAvailable = in.available();
if (bytesAvailable > 0) {
    byte[] data = new byte[bytesAvailable];
    in.read(data);
}
```

当你完成对输入/输出流的读写时，应该通过调用 `close` 方法来关闭它，这个调用会释放掉十分有限的操作系统资源。如果一个应用程序打开了过多的输入/输出流而没有关闭，那么系统资源将被耗尽。关闭一个输出流的同时还会冲刷用于该输出流的缓冲区：所有被临时置于缓冲区中，以便用更大的包的形式传递的字节在关闭输出流时都将被送出。特别是，如果不关闭文件，那么写出字节的最后一个包可能将永远也得不到传递。当然，我们还可以用 `flush` 方法来人为地冲刷这些输出。

即使某个输入/输出流类提供了使用原生的 `read` 和 `write` 功能的某些具体方法，应用系统的程序员还是很少使用它们，因为大家感兴趣的数据可能包含数字、字符串和对象，而不是原生字节。

我们可以使用众多的从基本的 `InputStream` 和 `OutputStream` 类导出的某个输入/输出类，而不只是直接使用字节。

## 输出流

如下所示，该类别的类决定了输出要去往的目标：字节数组（但不是`String`，当然，你也可以用字节数组自己创建）、文件或管道。

另外，`FilterOutputStream` 为 “装饰器” 类提供了一个基类；“装饰器” 类把属性或者游泳的接口与输出流连接了起来，这些稍后讨论。

继承 `OutputStream` 类型的有：

|类|功能|构造器参数|使用|
|:---:|:---:|:---:|:---:|
|ByteArrayOutputStream|在内存中创建缓存区。所有送往“流”的数据都要放置在此缓存区|||
|FileOutputStream|用于将信息写入文件|||
|FilterOutputStream|抽象类，作为“装饰器”的接口。其中，“装饰器”为其它的`OutputStream`类提供有用的功能|||
|ObjectOutputStream||||
|SequenceOutputStream|将两个或多个`InputStream`对象转换成一个`InputStream`|||
|StringBufferOutputStream|将`String`转换成`InputStream`|||
|PipedOutputStream|任何写入其中的信息都会自动作为相关`PipedInputStream`输出。实现“管道化”概念|||

## 添加属性和有用的接口

Java I/O 类库需要多种不同功能的组合，这正是使用装饰器模式的原因所在。而之所以存在 `Filter I/O`类，是因为让抽象类 `Filter I/O`作为所有装饰器类的基类。装饰器必须具有和它所装饰对象相同的接口，但它也可以扩展接口，不过这种情况只发生在个别 `Filter I/O` 类中。

但是，装饰器模式也有一个缺点：在编写程序的时候，它给我们带来了相当多的灵活性（因为我们可以很容易地对属性进行混搭），但它同时也增加了代码的复杂性。Java I/O 类库操作不便的原因在于：我们必须创建许多类（“核心” I/O 类型加上所有的装饰器）才能得到我们所希望的单个 I/O 对象。

`FilterInputStream/FilterOutputStream` 是用来提供装饰器类接口以控制特定输入/输出流 `InputStream/OutputStream` 的两个类，它它们的名字并不是很直观。`FilterInputStream/FilterOutputStream` 是从 I/O 类库中的基类 `InputStream/OutputStream` 派生而来，这两个类是创建装饰器的必要条件（这样它们才能为所有被装饰的对象提供统一接口）。

## 通过 FilterInputStream 从 InputStream 读取

`FilterInputStream` 类能够完成两件截然不同的事情。其中，`DataInputStream` 允许我们读取不同的基本数据类型和 `String` 类型的对象（所有方法都以 “read” 开头，例如 readByte()、readFloat()等等）。搭配其对应的 `DataOutputStream`，我们就可以通过数据“流”将基本数据类型的数据从一个地方迁移到另一个地方。具体是那些“地方”由输入流的类决定。

其它 `FilterInputStream` 类则在内部修改 `InputStream` 的行为方式：是否缓冲，是否保留它所读过的行（允许我们查询行数或设置行数），以及是否允许把单个字符推回输入流等等。最后两个类看起来就像是为了创建编译器提供的（它们被添加进来可能是为了对“用 Java 构建编译器”实现提供支持），因此我们在一般编程中不会用到它们。

在实际应用中，不管连接的是什么 I/O 设备，我们基本上都会对输入进行缓冲。所以当初 I/O 类库如果能默认都让输入进行缓冲，同时将无缓冲输入作为一种特殊情况（或者只是简单地提供一个方法调用），这样会更加合理，而不是像现在这样迫使我们基本上每次都得手动添加缓冲。

下表是 `FilterInputStream` 的类型

|类|功能|构造器参数|使用|
|:---:|:---:|:---:|:---:|
|DataInputStream|与 `DataOutputStream` 搭配使用，按照移植方式从流读取基本数据类型(int/char/long 等)|||
|BufferedInputStream|使用它可以防止每次读取时都得进行实际写操作。代表“使用缓冲区”|||
|LineNumberInputStream|跟踪输入流中的行号，可调用`getLineNumber()`和`setLineNumber(int)`|||
|PushbackInputStream|具有能弹出一个字节的缓冲区，因此可以将读到的最后一个字符回退|||

## 通过 FilterOutputStream 向 OutputStream 写入

与 `DataInputStream` 对应的是 `DataOutputStream`，它可以将各种基本数据类型和 `String` 类型的对象格式化输出到“流”中，。这样一来，任何机器上的任何 `DataInputStream` 都可以读出它们。所有方法都以 `write` 开头，例如 `writeByte()`、`writeFloat()` 等等。

`PrintStream` 最初的目的就是为了以可视化格式打印所有基本数据类型和 `String` 类型的对象。这和 `DataOutputStream` 不同，后者的目的是将数据元素置入“流”中，使 `DataInputStream` 能够可移植地重构它们。

`PrintStream` 内有两个重要方法：`print()` 和 `println()`。它们都被重载了，可以打印各种各种数据类型。`print()` 和 `println()` 之间的差异是，后者在操作完毕后会添加一个换行符。

`PrintStream` 可能会造成一些问题，因为它捕获了所有 `IOException`（因此，我们必须使用 `checkError()` 自行测试错误状态，如果出现错误它会返回 `true`）。另外，`PrintStream` 没有处理好国际化问题。这些问题都在 `PrintWriter` 中得到了解决，这在后面会讲到。

`BufferedOutputStream` 是一个修饰符，表明这个“流”使用了缓冲技术，因此每次向流写入的时候，不是每次都会执行物理写操作。我们在进行输出操作的时候可能会经常用到它。

下表是 `FilterOutputStream` 类型

|类|功能|构造器参数|使用|
|:---:|:---:|:---:|:---:|
|DataOutputStream|与 `DataInputStream` 搭配使用，因此可以按照移植方式向流中写入基本数据类型(int/char/long 等)|||
|PrintStream|用于产生格式化输出。其中`DataOutputStream`处理数据的存储，`PrintStream`处理显示|||
|BufferedOutputStream|使用它以避免每次发送数据时都进行实际的写操作。代表“使用缓冲区”。可以调用`flush()`清空缓冲区|||

## Reader 和 Writer

Java 1.1 对基本的 I/O 流类库做了重大的修改。你初次遇到 `Reader` 和 `Writer` 时，可能会以为这两个类是用来代替 `InputStream` 和 `OutputStream` 的，但实际上并不是这样。尽管一些原始的 “流” 类库已经过时（如果使用它们，编译器会发出警告），但是 `InputStream` 和 `OutputStream` 在面向字节 I/O 这方面仍然发挥着及其重要的作用，而 `Reader` 和 `Writer` 则提供兼容 `Unicode` 和面向字符 I/O 的功能。另外：

1. Java 1.1 往 `InputStream` 和 `OutputStream` 的继承体系中又添加了一些新类，所以这两个类显然是不会被取代的。
2. 有时我们必须把来自 “字节” 层级结构中的类和来自 “字符” 层次结构中的类结合起来使用。为了达到这个目的，需要用到 “适配器（adapter）类”：`InputStreamReader` 可以把 `InputStream` 转换为 `Reader`，而 `OutputStreamWriter` 可以把 `OutputStream` 转换为 `Writer`。

设计 `Reader` 和 `Writer` 继承体系主要是为了国际化。老的 I/O 流继承体系仅支持 8 比特的字节流，并且不能很好地处理 16 比特的 `Unicode` 字符。由于 `Unicode` 用于字符国际化（Java 本身的 `char` 也是 16 比特的 `Unicode`），所以添加 `Reader` 和 `Writer` 继承体系就是为了让所有的 I/O 操作都支持 `Unicode`。另外，新类库的设计使得它的操作比就类库要快。

## 数据的来源和去处

几乎所有原始的 Java I/O 流类都有相应的 `Reader` 和 `Writer` 类来提供原生的 `Unicode` 操作。但是在某些场合，面向字节的 `InputStream` 和 `OutputStream` 才是正确的解决方案。特别是 `java.util.zip` 类库就是面向字节而不是面向字符的。因此，最明智的做法是尽量尝试使用 `Reader` 和 `Writer`，一旦代码没法成功编译，你就会发现此时应该使用面向字节的类库了。

下表展示了在两个继承体系中，信息的来源和去处（即数据物理上来自哪里又去向哪里）之间的对应关系：

|来源与去处：Java 1.0 类|相应的 Java 1.1 类|
|:---:|:---:|
|InputStream|Reader <br/> 适配器： InputStreamReader|
|OutputStream|Writer <br/> 适配器：OutputStreamWriter|
|FileInputStream|FileReader|
|FileOutputStream|FileWriter|
|StringBufferInputStream（已弃用）|StringReader|
|（无相应的类）|StringWriter|
|ByteArrayInputStream|CharArrayReader|
|ByteArrayOutputStream|CharArrayWriter|
|PipedInputStream|PipedReader|
|PipedOutputStream|PipedWriter|

总的来说，这两个不同的继承体系中的接口即便不能说完全相同，但也是非常相似的。

## 更改流的行为

对于 `InputStream` 和 `OutputStream` 来说，我们会使用 `FilterInputStream` 和 `FilterOutputStream` 的装饰器子类来修改“流”以满足特殊需要。`Reader` 和 `Writer` 的类继承体系沿用了相同的思想——但是并不完全相同。

在下表中，左右之间对应关系的近似程度现比上一个表格更加粗略一些。造成这种差别的原因是类的组织形式不同，`BufferedOutputStream` 是 `FilterOutputStream` 的子类，但 `BufferedWriter` 却不是 `FilterWriter` 的子类（尽管 `FilterWriter` 是抽象类，但却没有任何子类，把它放在表格里只是占个位置，不然你可能奇怪 `FilterWriter` 上哪去了）。然而，这些类的接口却又十分相似。

|过滤器：Java 1.0 类|相应的 Java 1.1 类|
|:---:|:---:|
|FilterInputStream|FilterReader|
|FilterOutputStream|FilterWriter（抽象类，没有子类）|
|BufferedInputStream|BufferedReader（也有`readLine()`）|
|BufferedOutputStream|BufferedWriter|
|DataInputStream|使用DataInputStream（如果必须用到`readLine()`，那你就得使用BufferedReader。否则，一般情况下就用DataInputStream|
|PrintStream|PrintWriter|
|LineNumberInputStream|LineNumberReader|
|StreamTokenizer|StringTokenizer（使用具有Reader参数的构造器）|
|PushbackInputStream|PushbackReader|

有一条限制需要明确：一旦使用 `readLine()`，我们就不应该用 `DataInputStream`（否则，编译时会得到使用了过时方法的警告），而应该使用 `BufferedReader`。除了这种情况之外的情形中，`DataInputStream` 仍是 I/O 类库的首选成员。

为了使用时更容易过渡到 `PrintWriter`，它提供了一个既能接受 `Writer` 对象又能接受任何 `OutputStream` 对象的构造器。`PrintWriter` 的格式化接口实际上与 `PrintStream` 相同。

Java 5 添加了几种 `PrintWriter` 构造器，以便在将输出写入时简化文件的创建过程，你马上就会见到他们。

其中一种 `PrintWriter` 构造器还有一个执行 `flush` 的选项。如果构造器设置了该选项，就会在每个 `println()` 调用之后，自动执行 `flush`。

## 未发生改变的类

有一些类在 Java 1.0 和 Java 1.1 之间未做改变。

|以下这些 Java 1.0 类在 Java 1.1 中没有相应类|
|:---:|
|DataOutputStream|
|File|
|RandomAccessFile|
|SequenceInputStream|

特别是 `DataOutputStream`，在使用时没有任何变化；因此如果想以可传输的格式存储和检索数据，请用 `InputStream` 和 `OutputStream` 继承体系。

## RandomAccessFile 类

`RandomAccessFile` 适用于由大小已知的记录组成的文件，所以我们可以使用 `seek()` 将文件指针从一条记录移动到另一条记录，然后对记录进行读取和修改。文件中记录的大小不一定都相同，只要我们能确定那些记录有多大以及它们在文件中的位置即可。

最初，我们可能难以相信 `RandomAccessFile` 不是 `InputStream` 或者 `OutputStream` 继承体系中的一部分。除了实现了 `DataInput` 和 `DataOutput` 接口（`DataInputStream` 和 `DataOutputStream` 也实现了这两个接口）之外，它和这两个继承体系没有任何关系。它甚至都不使用 `InputStream` 和 `OutputStream` 类中已有的任何功能。它是一个完全独立的类，其所有的方法（大多数都是 `native` 方法）都是从头开始编写的。这么做是因为 `RandomAccessFile` 拥有和别的 I/O 类型本质上不同的行为，因为我们可以在一个文件内向前和向后移动。在任何情况下，它都是自我独立的，直接继承自 `Object`。

从本质上来讲，`RandomAccessFile` 的工作方式类似于把 `DataIunputStream` 和 `DataOutputStream` 组合起来使用。另外它还有一些额外的方法，比如使用 `getFilePointer()` 可以得到当前文件指针在文件中的位置，使用 `seek()` 可以移动文件指针，使用 `length()` 可以得到文件的长度。另外，其构造器还需要传入第二个参数（和 C 语言中的 `fopen()` 相同）用来表示我们是准备对文件进行 “随机读”（r）还是“读写”（rw）。它并不支持只写文件，从这点来看，如果当初 `RandomAccessFile` 能设计成继承自 `DataInputStream`，可能也是个不错的实现方式。

在 Java 1.4 中，`RandomAccessFile` 的大多数功能（但不是全部）都被 nio 中的内存映射文件（mmap）取代。

# 字节流

字节流是继承自输入字节流`InputStream`与输出字节流`OutputStream`，它们都是抽象类，是字节流的父类。

`InputStream`常见子类有：

`FileInputStream`从文件中读取信息
`ByteArrayInputStream`字节数组输入流
`ObjectInputStream`序列化时使用一般和ObjectOutputStream一起使用
`FilterInputStream`过滤输入流，为基础的输入流提供一些额外的操作。

# 字符流

`Reader`类是Java IO中所有`Reader`的基类。`Writer`类是Java IO中所有`Writer`的基类。


# 总结

Java 的 I/O 流类库的确能够满足我们的基本需求：我们可以通过控制台、文件、内存块，甚至因特网进行读写。通过继承，我们可以创建新类型的输入和输出对象。并且我们甚至可以通过重新定义 “流” 所接受对象类型的 `toString()` 方法，进行简单的扩展。当我们向一个期望收到字符串的方法传送一个非字符串对象时，会自动调用对象的 `toString()` 方法（这是 Java 中有限的 “自动类型转换” 功能之一）。

在 I/O 流类库的文档和设计中，仍留有一些没有解决的问题。例如，我们打开一个文件用于输出，如果在我们试图覆盖这个文件时能抛出一个异常，这样会比较好（有的编程系统只有当该文件不存在时，才允许你将其作为输出文件打开）。在 Java 中，我们应该使用一个 `File` 对象来判断文件是否存在，因为如果我们用 `FileOutputStream` 或者 `FileWriter` 打开，那么这个文件肯定会被覆盖。

I/O 流类库让我们喜忧参半。它确实挺有用的，而且还具有可移植性。但是如果我们没有理解“装饰器”模式，那么这种设计就会显得不是很直观。所以，它的学习成本相对较高。而且它并不完善，比如说在过去，我不得不编写相当数量的代码去实现一个读取文本文件的工具——所幸的是，Java 7 中的 nio 消除了此类需求。

一旦你理解了装饰器模式，并且开始在某些需要这种灵活性的场景中使用该类库，那么你就开始能从这种设计中受益了。到那时候，为此额外多写几行代码的开销应该不至于让人觉得太麻烦。但还是请务必检查一下，确保使用文件一章中的库和技术没法解决问题后，再考虑使用本章的 I/O 流库。

