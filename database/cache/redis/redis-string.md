# Redis - String

1. 能表达 3 中类型：字符串，整数，和浮点数。根据场景相互间自动转型，并且根据需求选取底层的承载方式。
2. value 内部以 int、sds 作为结构存储。int 存放整型数据，sds 存放字节、字符串和浮点型数据。
3. sds 内部结构：\(1\).  用 buf 数组存储字符串内容，但数组的长度会大于所存储内容的长度。会有一个专门存放“\0”（C 标准库）作为结尾，还有预留多几个空的（即 free 区域），当 append 字符串的长度小于free 区域，则 sds 不会重新申请内存，直接使用free 区域。（2）.扩容：当字符串的操作完成后与其的串长度小于 1 M 时，扩容后的 buf 数组大小= 预期长度\*2+1；若大于 1 M，则 buf 总是会预留 1 M 的 free 空间。（3）.value 对象通常具有两个内存部分，redisObject 部分和 redisObject 的ptr 指向的 sds 部分。创建 value 对象时，通常需要为 redisObject 和 sds 申请两次内存，单对于短小的字符串，可以把两者连续存放，所以可以一次性把两者的内存一起申请了。



1. 
