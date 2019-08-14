---
title: set
date: 2019-02-20 21:27:17
tags: DataStruct
index_img: ../post_img/set.jpeg
---

## Set

set 集合特点：元素不可重复

Set 集合常用子类：

 1. HashSet 集合：底层数据结构是哈希表（是一个元素为链表的数组）
 2. TreeSet 集合：底层数据结构是红黑树（是一个自平衡的二叉树），保证元素的排序方式
 3. LinkedHashSet 集合：底层数据结构是由哈希表和链表组成的

### Set 接口

Set 不允许包含相同的元素，如果试图把两个相同的元素加入同一个集合中，add 方法返回 false。Set 判断两个对象相同不是使用 == 运算符，而是根据 equals 方法。也就是说，只要两个对象用 equals 比较后返回 true，Set 就不会接受这两个对象。

### HashSet

不能保证元素的排列顺序，顺序有可能发生变化，不是同步的，集合元素可以是 null，但只能放入一个 null。底层是哈希表（是一个元素为链表的数组）。HashSet 其实使用 HashMap 实现的，HashMap 是 Map 接口的实现类。调用 HashSet 的 add 方法，其实就是在调用 HashMap 中的 put 方法，put 方法主要涉及两个方面。

1. 对象的 hash 值，通过调用 hash() 得到，这个方法是由 hashCode() 经过操作实现的，由 hashCode() 的值控制。
2. 创建了 hash 表，hash 表会将每一个 hash 值收入，然后比较：

   1. 先在 hash 表中查找看是否有这个 hash 值（第一次比较，看 hash 表中是否有当前元素的 hahs 值（这个值有 hahsCode 操作的得到），如果没有，直接将这个 hahs 值对应的对象添加到 HashSet 中，如果有，还需要进行第二次比较）。
   2. 如果 hash 表有这个 hash 值，那么获取列表中的这个 hash 对应的对象，如果这两个对象的地址值(e.key == key)或者key.equal(e.key)。（第二次比较，如果两个对象的 hahs 值相同，还是不能认为是同一个对象，还要比较两个对象的地址值，或者是 equals() 这里是一个 ||，只要有一个满足相等，就可以认为是同一个元素，不添加）。

3. 为什么存储字符串的时候，字符串相同的时候，只存储了一个？因为 String 类重写了 hashCode 和 equals，而 String 类的 hashCode 值和 equals 结果都是由字符串的内容决定的。

4. 如果 HashSet 中收入的是自定义对象，那如何实现唯一呢？

   1. 通过上述的 String 类的分析，我们知道，HashSet 中实现唯一的两个方法，先是比较 hash 值（这个由 hahsCode 值控制），如果这个值相同，那么再比较地址值或者 equals，所以我们可以通过重写 hahsCode 和 queals 来实现对自定义对象的唯一性判断。
   2. 只要重写了 equals，让这个方法比较的是对象的内容，那么就可以排除掉相同的对象。让 hashCode 返回一个常数，这样 hash 就相等了，然后通过 equals 判断，但这样会导致每一个新对象都要和老对象进行比较，太麻烦，所以我们可以仿照 String 类中对 hashCode 重写的方式，同样的有对象的成员变量来决定这个对象的 hashCode 值，equals 也是通过比较对象的成员变量是否相等。这样既可以得到最后的结论。

### TreeSet

底层数据结构是红黑树（是一个自平衡的二叉树）。TreeSet 是如何保证元素排列方式，以及元素的唯一性呢。

TreeSet 是 SortedSet 接口的唯一实现类，TreeSet 可以确保集合元素处于排序状态，TreeSet 支持两种排序方式，自然排序和定制排序，其中自然排序为默认的排序方式。向 TreeSet 中加入的应该是同一类的对象。TreeSet 判断两个对象不相等的方式是两个对象通过 equals 方法返回 false 或者是通过 CompareTo 方法比较没有返回 0。

#### 自然排序

自然排序使用要排序元素的 CompareTo(Object object) 方法来比较元素之间的大小关系，然后将元素按照升序排列。Java 提供了一个 Comparable 接口，改接口中定义了一个 compareTo(Object obj) 方法，该方法返回一个整数值，实现了该接口的对象就可以比较大小。

obj.compareTo(obj2) 方法如果返回 0，则说明被比较的两个对象相等，如果返回一个整数，则表明 obj1 大于 obj2，如果是负数，则表明 obj1 小于 obj2。

如果我们将两个对象的 equals 方法总是返回 false，则这两个对象的 compareTo 方法总是返回 0。

#### 定制排序

自然排序是根据集合元素的大小，以升序排列，如果要定制排序，应该使用 Comparatoer 接口，实现 int compare(a,b)方法。

### LinkedLisst

LinkedHashSet 集合同样是根据元素的 hashCode 值来决定元素的存储位置，但是它同时使用链表维护元素的次序。这样使得元素看起来像是以插入顺序保存的，也就是说，当遍历该集合时，LinkedListSet 将以元素的添加顺序访问集合的元素。LinkedListSet 在迭代访问 Set 中的全部元素时，性能比 HashSet 好，但是插入时性能稍微逊色于 HashSet。