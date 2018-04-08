a
## Java IO中的设计模式

java语言 I/O库的设计中，主要使用了两个结构模式，即装饰模式和适配器模式

java语言的I/O库是对各种常见的流源、流汇以及处理过程的抽象化。客户端的java 程序不必知道最终的的流源、流汇是磁盘上的文件还是一个数组，或者是一个线程；也不比插手到诸如数据是否缓存、可否按照行号读取等处理的细节中去。要理解java I/O 这个庞大而复杂的库，关键是掌握两个对称性和两个设计模式

**java I/O库的两个对称性**
 java I/O库具有两个对称性，它们分别是：

- 输入-输出对称：比如InputStream和OutputStream各自占据Byte流的输入和输出的两个平行的等级结构的根部；而Reader和Writer各自占据Char流的输入和输出的两个平行的等级结构的根部。
- byte-char对称：InputStream和Reader的子类分别负责byte和插入流的输入；OutputStream和Writer的子类分别负责byte和Char流的输出，它们分别形成平行的等级结构

###装饰模式的应用

由于java I/O库需要很多性能的各种组合，如果这些性能都是用继承来实现，那么每一种组合都需要一个类，这样就会造成大量行重复的类出现。如果采用装饰模式，那么类的数目就会大大减少，性能的重复也可以减至最少。因此装饰模式是java I/O库基本模式。装饰模式的引进，造成灵活性和复杂性的提高。因此在使用 java I/O 库时，必须理解java I/O库是由一些基本的原始流处理器和围绕它们的装饰流处理器所组成的

**InputStream类型中的装饰模式**

InputStream有七个直接的具体子类，有四个属于FilterInputStream的具体子类

原始流处理器接收一个Byte数组对象、String对象、FileDescriptor对象或者不同类型的流源对象（就是前面所说的原始流源），并生成一个InputStream类型的流对象。在InputStream类型的流处理器中，原始流处理器包括以下四种

所谓链接流处理器就是可以接受另一个(同种类的）流对象(就是链接流源）作为流源，并对之进行功能扩展的类。InputStream类型的链接流处理器包括以下几种，它们接受另一个InputStream对象作为流源

- ByteArrayInputStream
- FileInputStream
- PipedInputStream
- StringBufferInputStream （已逐渐废弃）
- FilterInputStream 称为过滤输入流，它将另一个输入流作为流源。这个类的子类包括以下几种
    + BufferInputStream 用来从硬盘将数据读入到一个内存缓冲区中，并从此缓冲区提供数据
    + DateInputStream 提供基于多字节的读取方法，可以读取原始数据类型的数据
    + LineNumberInputStream 提供带有行计算功能的过滤输入流
    + PushbackInputStream 提供特殊的功能，可以将已读取的直接“推回”输入流中
- ObjectInputStream 可以将使用ObjectInputStream串行化的原始数据类型和对象重新并行化
- SequenceInputStream可以将两个已有的输入流连接起来，形成一个输入流，从而将多个输入流排列构成一个输入流序列

必须注意的是，虽然PipedInuptStream接受一个流对象PipedOutputStream作为流的源，但是PipedOutputStream流对象的类型不是InputStream，因此PipedInputStream流处理器仍属于原始流处理器

**装饰模式的各个角色**

- 抽象构件（Component）角色 由InputStream扮演。这是一个抽象类，为各种子类型处理器提供统一的接口
- 具体构建（Concrete Component）角色 由ByteArrayInputStream、FileInputStream、PipedInputStream以及StringBufferInputStream等原始流处理器扮演。它们实现了抽象构建角色所规定的接口，可以被链接流处理器所装饰
- 抽象装饰（Decorator）角色 由FilterInputStream扮演。它实现了InputStream所规定的接口
- 具体装饰（Concrete Decorator）角色 由几个类扮演，分别是DateInputStream、BufferedInputStream 以及两个不常用到的类LineNumberInputStream和PushbackInputStream

**OutputStream 类型中的装饰模式**

outputStream是一个用于输出的抽象类，它的接口、子类的等级结构、子类的功能都和InputStream有很好的对称性。在OutputStream给出的接口里，将write换成read就得到了InputStream的接口，而其具体子类则在功能上面是平行的。

outputStream有5个直接的具体子类，加上三个属于FilterInputStream的具体子类，一共有8个具体子类

- ByteArrayOutputStream 为多线程的通信提供缓冲区操作功能。输出流的汇集是一个byte数组
- FileOutputStream 建立一个与文件有关的输出流。输出流的汇集是一个文件对象
- PipedOutputStream 可以与PipedInputStream配合使用，用于向一个数据管道输出数据
- FilterOutputStream 称为过滤输出流，它将另一个输出流作为流汇。这个类的子类有如下几种
    + BufferedOutputStream 用来向一个内存缓冲区中写数据，并将此缓冲区的数据输入到硬盘中
    + DataOutputStream 提供基于多字节的写出方法，可以写出原始数据类型的数据
    + PrintStream 用于产生格式化输出。System.out 静态对象就是一个PrintStream
- ObjectOutputStream 可以将原始数据类型和对象串行化

具体各个类扮演的角色 可以参考上面类比

**Reader类型中的装饰模式**

- CharArrayReader：为多线程的通信提供缓冲区操作功能。
- InputStreamReader：
    + 这个类有一个子类--FileReader。
- PipedReader：可以与PipedOutputStream配合使用，用于读入一个数据管道的数据。
- StringReader：建立一个与文件有关的输入流。
- BufferedReader 用来从硬盘将数据读入到一个内存缓冲区，并从此缓冲区提供数据
    + LineNumberReader
- FilterReader：成为过滤输入流，它将另一个输入流作为流的来源
    + PushbackReader，提供基于多字节的读取方法，可以读取原始数据类型的数据

这里的装饰角色 由BufferedReader和FilterReader扮演。这两者有着与Reader相同的接口，它们 分别给出两个装饰角色的等级结构，第一个给出LineNumberReader作为具体装饰角色，另一个给出PushbackReader 作为具体装饰角色。

**Writer类型中的装饰模式**

- CharArrayWriter
- OutputStreamWriter
    + FileWrite 子类
- PipedWriter 可以和PipedOutputStream配合使用，用于读如果一个数据管道的数据
- StringWriter：想一个StringBuffer写出数据
- BufferedWriter
- FilterWriter 称为过滤输入流，它将另一个输入流作为流的来源。**这是一个没有子类的抽象类**
- PrintWriter 支持格式化的文字输出。
这里的装饰角色 由BufferedWriter、FilterWriter、PrintWriter扮演，它们有着与Write
相同的接口。

###适配器模式的应用

InputStream类型的原始流处理器是适配器模式的应用。
ByteArrayInputStream是一个适配器类 。ByteArrayInputStream继承了InputStream的接口，而封装了一个byte数组。换而言之，它将一个byte数组的接口适配成了InputStream流处理器的接口

在OutputStream类型中，所有的原始流处理器都是适配器类。ByteArrayOutputStream是一个适配器类。ByteArrayOutputStream继承了OutputStream类型，同事持有一个对byte数组的引用。它把一个byte数组的接口适配成OutputStream类型的接口，因此也是一个对象类型的适配器模式的应用
