---
description: 写时复制容器
---

# CopyOnWrite\(COW\)

### CopyOnWrite\(COW\)

容器添加新元素时，不直接在当前容器添加，而是先将容器复制，在新容器中添加新元素，然后将原容器指针指向新容器。（延时懒惰策略）。

1.  CopyOnWriteArrayList 
2. CopyOnWriteArraySet

#### 应用场景

COW 并发容器用于读多写少的并发场景。比如比如白名单，黑名单，商品类目的访问和更新场景，假如我们有一个搜索网站，用户在这个网站的搜索框中，输入关键字搜索内容，但是某些关键字不允许被搜索。这些不能被搜索的关键字会被放在一个黑名单当中，黑名单每天晚上更新一次。当用户搜索时，会检查当前关键字在不在黑名单当中，如果在，则提示不能搜索。实现代码如下：

```java
public class CopyOnWriteMap<K,V> implements Map<K,V>,Cloneable{ 
      private volatile Map<K,V> internalMap;
      public CopyOnWriteMap(){ 
           internalMap =new HashMap<K,V>; 
           }
      public V put(K key,V value){ 
          synchronized(this){ 
                Map<K,V> newMap =new HashMap<K,V>(internalMap); 
                V val =newMap.put(key,value); 
                internalMap =newMap; return val; 
                } 
          }
      public V get(Object key){
         return internalMap.get(key); 
         }
      public void putAll(Map<? extends K,? extends V>newData){
         synchronized(this){ Map<K,V> newMap =new HashMap<K,V>(internalMap);
         newMap.putAll(newData); 
         internalMap=newData; 
         }
       }
 }
```

#### COW 缺点

1. 内存占用问题。 因为 COW 是写时复制机制，所以进行写操作，内存里会同时存在两个对象的内存，旧对象和新写入的对象。如果对象太大，会造成频繁的 Yong GC 和 Full GC，导致响应时间过长可以通过压缩容器元素来解决方法：减少内存消耗，或使用 CurrentHashMap.
2. 数据的最终一致性 COW 只保证数据的最终一致性，而不保证数据的实时一致性。

#### Tips：

1. 减少扩容开销，根据实际需要，初始化 CopyOnWriteMap 的大小，避免写时 CopyOnWriteMap 扩容开销。
2. 使用批量添加。每次根据添加，容器会进行复制，所以减少添加次数可以减少容器的复制次数。 

{% file src="../../.gitbook/assets/copyandwrtie.md" %}

