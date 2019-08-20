# Redis 底层原理

Redis 五种对象的类型

| 类型常量 | 对象的名称 |
| :--- | :--- |
| String | 字符串对象 |
| List | 列表对象 |
| Hash | 哈希对象 |
| Set | 集合对象 |
| Zset | 有序集合对象 |

### 底层数据结构

| 编码常量 | 编码所对应的底层数据结构 |
| :--- | :--- |
| int | long 类型的整数 |
| ENCODING\_EMBSTR | embstr 编码的简单动态字符串 |
| RAW | 简单动态字符串 |
| ht | 字典 |
| linkedlist | 双端链表 |
| ziplist | 压缩列表 |
| intset | 整数集合 |
| skiplist	 | 跳跃表和字典 |

