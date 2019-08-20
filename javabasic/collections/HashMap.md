---
title: HashMap
date: 2019-02-13 17:14:55
tags: DataStruct
index_img: ../post_img/taoyifenxi.jpeg
---

## HashMap

> 线程不安全，安全集合为 HashTable，ConcurrentHashMap。

### HashMap 对比其他数据结构的性能

#### 数组

采用一段连续的存储单元来存储数据。对于指定下标的查找，时间复杂度为 O(1)。通过给定值进行查找，需要遍历数组，逐一对比给定关键字和数组元素，时间复杂度为 O(n)。当然，对于有序数组，可以采用二分查找，插值查找，斐波那契查找等方式，可以将复杂度提高为 O(logn)。对于一般的插入删除操作，涉及数组元素的移动，其平均复杂度也为 O(n)。

#### 线性链表

对于链表的新增，删除等操作（在找到指定操作位置后），仅需处理节点间的引用即可，时间复杂度为 O(1)，而查找操作需要遍历链表，逐一对比，复杂度为 O(n)。

#### 二叉树

对于一棵相对平衡的有序二叉树，对其进行插入、查找、删除等操作，平均复杂度为 O(logn)。

#### 哈希表

相比上述的几种数据结构，在哈希表中进行添加、删除、查找等操作，性能十分之高，不考虑哈希冲突的情况下，仅需要一次定位即可完成，时间复杂度为 O(1)。

比如我们要新新增或查找某个元素，我们通过把当前元素的关键字通过某个函数映射到数组中的某个位置，通过数组下标就可以完成一次定位的操作。

```  Java
   存储位置 =  f(关键字)
```
其中，我们称 f 为**哈希函数**。这个函数的设计直接影响哈希表的性能好坏。

![Insert](../_posts/HashMap/insert.png)

查找操作同理，先通过哈希函数计算出实际存储地址，然后从数组中对应的地址取出即可。

#### 哈希冲突

插入元素时，通过 hash() 方法获得了地址时，发现地址已经被占用了，即产生了**哈希冲突**（哈希碰撞）。数组是一块地址连续且定长的内存空间，再好的哈希函数也不能保证存储地址绝对不发生冲突。

解决方法；**开放定址法**，**再哈希法**、链地址法**，Hashmap 采用的链地址法，即：数组 + 链表。

``` Java
 transient Entry<K,V>[] table = (Entry<K,V>)EMPTY_TABLE;

 static class Entry<K,V> implements Map.Entry<K,V>{
     final K key;
     V value;
     Entry<K,V> next;// 存储指向下一个 Entry 的引用，单链表结构
     int hash; // 对 key 使用 hashcode 值进行 hash 运算之后得到的值，存储在 Entry 中，避免重复计算

     Entry(int h,K k,V v,Entry<K,V>){
         value = v;
         next = n;
         key = k;
         hash =h;
     }
 }
```

所以 HashMap 的大致结构如下：

![Entry Struct](../_posts/HashMap/entry.png)

HashMap 是由数组 + 链表组成的，数组是 HashMap 的主体，链表则是为了解决哈希冲突而存在的，如果定位的数组位置不含链表（当前 Entry 的 next 指向 null），那么对于查询、添加等操作很快，仅需要一次寻址即可；如果定位到的数组包含链表，对于添加操作，其时间复杂度为 O(n),首先遍历链表，存在即覆盖，否则新增。对于查找操作，仍然需要遍历链表，然后通过 key 对象的 equals 方法逐一对比。所以，HashMap 中的链表结构越少，性能越好。

``` Java
 // 实际存储 k-V 键值对的个数
 transient int size;

 // 当 table = {} 时，该值为初始容量（默认容量为 16）；当 table 被填充时，即，为 table 分配好空间后，threshold 一般为 capacity*loadFactor。HashMap 在扩容时需要参考 threshold。
 int threshold;


 // 负载因子，代表 table 的填充度，默认值 0.75
 final float loadFactor;

 // 用于快速失败，因为 HashMap 非线程安全，在对 HashMap 进行迭代时，如果期间其他线程的参与导致 HashMap 的结构发生了变化（比如 Put remove 等操作），需要抛出异常 ConcurrentModificationException
 transient int modCount;
```

HashMap 有 4 个构造器，其他构造器如果用户没有传入 initialCapacity 和 loadFactor 两个参数，就会使用默认值。initialCapacity 默认值为 16，loadFactory 默认值 0.75。

``` Java
 public HashMap(int initialCapacity,float loadFactor){

     // 对传人的 initialCapacity 进行校验，最大不能超过 MAVMUM_CAPACITY = 1<<30(2 的 30 次方)
     if(initialCapacity <0){
         throw new IllegalArgumentException("Illegal initial capacity:"+initialCapacity);
     }
     if(initialCapacity > MAXMUM_CAPACITY){
         initialCapacity = MAXMUM_CAPACITY;
     }
     if(loadFactor <= 0||Float.isNaN(loadFactor)){
         throw new IllegalArgumentException("Illegal load factor:"+ loadFactor);
     }
     this.loadFactor = loadFactor;
     threshold = initialCapacity;

     // init() 在 HashMap 中没有实际的实现，不过在其子类如 LinkedHashMap 中就会有对应的实现。
     init();
 }
```

以上代码说明，在常规的构造器中，并没有为数组 table 分配内存空间（有一个入参为指定 Map 的构造器例外），反而是在执行 put 操作时才真正构建 table 数组。

以下为 Put 的实现：

``` Java
 public V put(K key, V value){
     // 如果 table 数组为空，则进行数组填充（为 table 分配实际内存空间），入参为 threshold，此时 threshold 为 initialCaptiy 的默认值（1<< 4(2 的 16 次方))
     if(table == EMPTY_TABLE){
         inflateTable(threshold);
     }

     // 如果 key 为 null，存储位置为 table[0],或 tabel[0] 的冲突链表上
     if(key == null){
         return putForNullKey(value);
     }

     // 对 key 的 hashcode 做进一步运算，确保散列均匀
     int hash = hash(key);
     // 获取在 table 中的实际位置
     int i = indexFor(hash,table.length);
     for(Entry<K,V> e = table[i];e!=null;e =e.next){
         // 如果该对应数据已经存在，执行覆盖操作，用新的 value 值替换旧 value 值，并返回旧 value 值。
         Object k;
         if(e.hash == hash && ((k = e.key)== key|| key.equals(k))){
             V oldValue = e.value;
             e.value = value;
             e.recordAccess(this);
             return oldValue；
         }
     }
     // 保证并发访问时，若 HashMap 内部结构发生变化，快速响应失败
     modCount ++;
     // 新增一个 entry 值
     addEntry(hash,key,value,i);
     return null;
 }
```

以下为 inflateTable():

``` Java
  private void inflateTable(int tosize){
      // capacity 一定是 2 的次幂
      int capacity  = roundUpToPowerOf2(tosize);
      // 此处为 threshold 赋值，取 capacity*loadFactory 和 MAXMUM_CAPACITY 的最小值，capacity 一定不会超过 MAXMUM_CAPACITY，除非 loadFactory 大于1；
      threshold = (int) Math.min(capacity * loadFactor,MAXMUM_CAPACITY +1);
      table = new Entry[capacity];
      initHashSeeAsNeeded(capacity);
  }
```

inflateTable() 用于为主干数组 table 在内存中分配存储空间，通过 roundUpToPowerOf2(toSize) 可以确保 capacity 大于或等于 toSize 最接近的 二次幂，比如， tosize = 13，则 capacity = 16； toSize = 16，capacity =16； toSize = 17，capacity =32；

``` Java
 private static int roundUpTOPowerOf2(int number){
     // assert number >= 0:"number must be non-negative"
     return number >= MAXMUM_CAPACITY
            ? MAXMUM_CAPACITY
            : (number>1)？Integer。highsetOnBit(number -1)<<1:1
 }
```

roundUpToPowerOf2 中的这段处理是的数组长度一定为 2 的二次幂，Integer。highestOneBit 是用来获取最左边的 bit（其他 bit 为 0）所代表的数值。

``` Java
 final int hash(Object k){
     int h = hashSeed;
     if(0!= h && k instanceof String ){
         return sun.misc.Hashing.stringHash32(String)k);
     }
     h ^= k.hashCode();
     h ^= (h >>> 20) ^ (h >>> 12);
     return h ^ (h >>> 7) ^ (h >>> 4);
 }
````

以上 hash 函数计算出的值，通过 indexFor 进一步处理来获取实际的存储位置。

``` Java
 static int indexFor(int h,int length){
     return h &(length -1);
 }
```

h&(length -1) 保证获取的 index 在一定数组范围内，举个例子，默认容量是 16，length-1 =15 ，h =18，转换成二进制计算为

``` Java
        1  0  0  1  0
    &   0  1  1  1  1
    __________________
        0  0  0  1  0    = 2
```

最终计算出 index = 2.有些版本的对于此处的技术会使用 取模运算，也能保证 index 在一定数组范围内，不过位运算对计算机来说，性能更高一些(HashMap 中有大量位运算)

![Entry Struct](../_posts/HashMap/hashcode.png)

``` Java
 void addEntry(int hash,K key, V value，int bucketIndex)｛
    if((size >= threshold)&&(null != table[buckentIndex]){
        resize( 2 * table.length);
        hash = (null != key)? hash(key):0;
        bucketIndex = indexFor(hash,table.length);
    }
    createEntry(hash,key,value,bucketIndex);
 ｝
```

当发生哈希冲突且 size 大于阀值时，需要进行数组扩容，扩容时，需要新建一个长度为之前数组 2 倍的新数组，然后将当前的 Entry 数组中的元素全部传过去，扩容后的新数组长度为之前的 2 倍，所以扩容是一个消耗资源的操作。

### 为什么 HashMap 的数组长度一定是 2 的次幂

```Java
void resize(int newCapacity){
    Entry[] oldTable = table;
    int oldCapacity = oldTable.length;
    if(oldCapacity == Integer.MAX_VALUE){
        return;
    }
    Entry[] newTable = new Entry[newCapacity];
    transfer(newTabel,initHashSeedAsNeeded(newCapacity));
    table = newTable;
    threshold =(int) Math.min(newCapacity * loadFactor,MAXMUM_CAPACITY +1);
}
```

如果数组进行扩容，数组长度发生变化，存储位置 index = h&(length -1), index 也会发生变化，需要重写计算 index

``` Java
 void transfer(Entry[] newTable,boolean rehash){
     int newCapacity = newTable.length;
     for(Entry<K,V> e:table){
         while(null != e){
             Entry<K,V> next =e.next;
             if(rehash){
                 e.hash =null == e.key ? 0:hash(key);
             }
             int i = indexFor(e.hash,newCapacity);

             e.next =newTable[i];
             newTable[i] = e;
             e =next;
         }
     }
 }
```

它会将老数组中的数据逐个遍历，传入新数组中，我们的数组索引位置的计算是通过对 key 值的 hashcode 进行 hash 扰乱运算后，再通过和 length-1 进行位运算得到最终数组索引位置

hashMap的数组长度一定保持2的次幂，比如16的二进制表示为 10000，那么length-1就是15，二进制为01111，同理扩容后的数组长度为32，二进制表示为100000，length-1为31，二进制表示为011111。从下图可以我们也能看到这样会保证低位全为1，而扩容后只有一位差异，也就是多出了最左位的1，这样在通过 h&(length-1)的时候，只要h对应的最左边的那一个差异位为0，就能保证得到的新的数组索引和老数组索引一致(大大减少了之前已经散列良好的老数组的数据位置重新调换)，个人理解。

![Entry Struct](../_posts/HashMap/weiyunsuan1.png)

 还有，数组长度保持2的次幂，length-1的低位都为1，会使得获得的数组索引index更加均匀，比如：

![Entry Struct](../_posts/HashMap/weiyunsuan2.png)

　　我们看到，上面的&运算，高位是不会对结果产生影响的（hash函数采用各种位运算可能也是为了使得低位更加散列），我们只关注低位bit，如果低位全部为1，那么对于h低位部分来说，任何一位的变化都会对结果产生影响，也就是说，要得到index=21这个存储位置，h的低位只有这一种组合。这也是数组长度设计为必须为2的次幂的原因。

![Entry Struct](../_posts/HashMap/weiyunsuan3.png)

　　如果不是2的次幂，也就是低位不是全为1此时，要使得index=21，h的低位部分不再具有唯一性了，哈希冲突的几率会变的更大，同时，index对应的这个bit位无论如何不会等于1了，而对应的那些数组位置也就被白白浪费了。

``` Java
  public V get(Object key){
      // 如果 key 为 null，则直接去 table[0] 处去检索即可
      if(key == null){
          return getForNullKey();
      }
      Entry<K,V> entry =getEntry(key);
      return null == entry? null: entry.getValue();
  }
```

get 通过 key 返回对应的 value，如果 key 为 null，直接去 table() 检索，getEntry():

``` Java
 final Entry<K,V> getEntry(Object key){
 if(size == 0){
     return null;
 }

 // 通过 key 的 hashCode 获取 hash 值
 int hash = (key== null)?0:hash(key);

 // indexFor(hash& length -1) 获取最终数组索引，然后遍历链表，通过 equals 方法找出对应记录
 for(Entry<K,V> e =table[indexFor(hash,tabel.length)]);
      e != null;
      e =e.next;){
          Object k;
          if(e.hash == hash && 
           ((k = e.key) == key || (key != null && key.equals(k)))))
           return e;
      }
      return null;
}
```

get(): key(hashCode) --> hash --> indexFor --> 最终索引位置，找到对应位置 table[i],再看是有链表，如果有，则遍历链表，通过 key 的 equals 方法查找对应激励。

要注意的是，有人觉得上面在定位到数组位置之后然后遍历链表的时候，e.hash == hash这个判断没必要，仅通过equals判断就可以。其实不然，试想一下，如果传入的key对象重写了equals方法却没有重写hashCode，而恰巧此对象定位到这个数组位置，如果仅仅用equals判断可能是相等的，但其hashCode和当前对象不一致，这种情况，根据Object的hashCode的约定，不能返回当前对象，而应该返回null。