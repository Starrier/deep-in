# Redis - List

list 类型的 value 对象内部以 linkedlist 或 ziplist 承载。当 list 的元素和单个元素的长度较小时，redis 会采用 ziplist 实现以减少内存占用，否则采用 linkedlist 结构。

linkedlist 内部实现是双向链表。在 list 中定义了头尾元素指针和列表的长度，是 pop、push 操作、lien 操作的复杂的是 O\(1\)。由于是链表，lindex 类的操作复杂度仍然是O（N）

ziplist 的内部结构

1. 所有内容被放置在连续的内存中，其中 zibytes 表示ziplist 的总长度，zltail 指向最末元素，zllen 表示元素个数，entry 表示元素自身内容，zlend 作为 ziplist 定界符。
2. rpush、rpop、llen ，复杂的为O（1），lpush、pop 操作由于涉及权利元素的一定，复杂的为O（N）

