# Java 12 新特性

1. Shenandoah：低暂停时间 GC

新增了一个名为 Shenandoah 的 GC 算法，通过与正在运行的 Java 线程同时进行 evacuation 工作来减少 GC 暂停时间。使用 Shenandoah 的暂停时间与堆大小无关，这意味着无论堆是 200 MB 还是 200 GB，都将具有相同的暂停时间

2. 微基准测试套件

3. Switch 表达式（预览功能）

扩展了 switch 语句，使其不仅可以作为语句（statement），还可以作为表达式（expression），并且两种写法都可以使用传统的 switch 语法，或者使用简化的“case L ->”模式匹配语法作用于不同范围并控制执行流。这些更改将简化日常编码工作，并为 switch 中的模式匹配（JEP 305）做好准备。

4. JVM 常量 API

5. 只保留一个 AArch64 实现

6. 默认类数据共享归档文件

7. 可中止的 G1Mixed GC

如果 G1Mixed GC 存在超出暂停目标的可能性，则使其可终止。

8. G1 及时返回未使用的已分配内存

