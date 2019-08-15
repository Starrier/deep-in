# Java  IO 中的设计模式

- 适配器模式
- 装饰器模式

Java IO 中存在一种流处理器，可以将一种流的输出作为另一个流的输入。这样做的好处是可以根据流的类型进行不同方式的处理。流源分类：

1. 数组，String，File 等，称为原始流源
2. 同样类型的流用做链接流类的流源，称为链接流源

Java IO 设计原则

java I/O 库是对各种常见的流源、流汇以及处理过程的抽象化。客户端 java 程序不必知道最终的的流源、流汇是磁盘上的文件还是一个数组，或者是一个线程；也不比插手到诸如数据是否缓存、可否按照行号读取等处理的细节中去。要理解 java I/O 这个庞大而复杂的库，关键是掌握两个对称性和两个设计模式。

Java IO 的两个对称性

1. 输入输出对称

比如InputStream和OutputStream各自占据Byte流的输入和输出的两个平行的等级结构的根部；而Reader和Writer各自占据Char流的输入和输出的两个平行的等级结构的根部。

2. byte char 对称

InputStream 和 Reader 的子类分别负责 byte 和插入流的输入；OutputStream 和 Writer 的子类分别负责 byte 和 Char 流的输出，它们分别形成平行的等级结构。

Java IO  的两个设计模式

1. 装饰器模式

在由InputStream、OutputStream、Reader和Writer代表的等级结构内部，有一些流处理器可以对另一些流处理器起到装饰作用，形成新的、具有改善了的功能的流处理器。

2. 适配器模式

在由InputStream、OutputStream、Reader和Writer代表的等级结构内部，有一些流处理器是对其他类型的流处理器的适配。这就是适配器的应用。