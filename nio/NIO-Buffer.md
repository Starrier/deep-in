# NIO Buffer

Java NIO 由以下核心部分组成

- Buffer
- Channel
- Selector

传统的 IO 操作面向数据流，即每次从流中读取一个或多个字节，直至完成，数据没有被缓存在任何地方。

NIO 操作面向缓存区，数据从 Channel 读取到 Buffer 缓存区，随后在 Buffer 中处理数据。

Buffer 缓存区，内部使用字节数组存储数据，并维护几个特殊变量，实现数据的反复利用。

1. mark： 初始值 -1，用于备份当前 position。
2. position： 初始值 0，position 表示当前可以写入或读取数据的位置，当写入或读取一个数据后，position 向前移动到下一个位置。
3. limit：写模式下，limit 表示最多能往 Buffer 里写多少数据，等于 capacity 值；读模式下，limit 表示最多可以读取多少数据。
4. capacity：缓存数组大小


mark() 将 position 复制给 mark

``` java
public final Buffer mark(){
    mark = position;
    return this;
}
```

reset()

``` java

```

Buffer 的实现类：

- ByteBuffer
- CharBuffer
- DoubleBuffer
- FloatBuffer
- IntBuffer
- IntBuffer
- LongBuffer
- ShortBuffer
- MappedByteBuffer