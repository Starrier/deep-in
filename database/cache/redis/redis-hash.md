# Redis Hash

## Hash 数据机构

``` c
/*Hash表一个节点包含Key,Value数据对 */
typedef struct dictEntry {
    void *key;
    union {
        void *val;
        uint64_t u64;
        int64_t s64;
        double d;
    } v;
    struct dictEntry *next; /* 指向下一个节点, 链接表的方式解决Hash冲突 */
} dictEntry;
 
/* 存储不同数据类型对应不同操作的回调函数 */
typedef struct dictType {
    unsigned int (*hashFunction)(const void *key);
    void *(*keyDup)(void *privdata, const void *key);
    void *(*valDup)(void *privdata, const void *obj);
    int (*keyCompare)(void *privdata, const void *key1, const void *key2);
    void (*keyDestructor)(void *privdata, void *key);
    void (*valDestructor)(void *privdata, void *obj);
} dictType;
 
typedef struct dictht {
    dictEntry **table; /* dictEntry*数组,Hash表 */
    unsigned long size; /* Hash表总大小 */
    unsigned long sizemask; /* 计算在table中索引的掩码, 值是size-1 */
    unsigned long used; /* Hash表已使用的大小 */
} dictht;
 
typedef struct dict {
    dictType *type;
    void *privdata;
    dictht ht[2]; /* 两个hash表,rehash时使用*/
    long rehashidx; /* rehash的索引, -1表示没有进行rehash */
    int iterators; /*  */
} dict;
```