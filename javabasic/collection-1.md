# Collection



![CollectionTree](../../.gitbook/assets/collectiontree.jpg)

## List

实现类：ArrayList 、LinkedList、Vector

List 中对象按照索引位置排序，可以有重复对象，允许按照对象正在集合中的索引位置检索对象，如通过 list.get\(i\) 方式来 获得 List 集合中的元素。**以特定次序存储元素，所以取出来的顺序和放入的顺序可能有所不同。**

## Set

实现类：HashSet、TreeSet

Set 中对象不按特定方式排序，并且没有重复对象。但它有些实现类能对集合中的对象按特定方式排序，如 TreeSet 类，它可以按照默认排序，也可以通过实现 java.util.Comparator&lt;Type&gt; 接口来自定义排序方式。

## Map

实现类：HashMap、TreeMap、HashTable

Map 中的每一个元素包含一个键对象和值对象，它们成对出现。键对象不能重复，值对象可以重复。

## 线程安全类

vector ： 比 arrayList 多个同步化机制（线程安全），因为效率低，现在已经不太使用。

statck ： 堆栈类，先进后出。

hashtable ： 比 hashmap 多个线程安全。

enumeration：枚举，相当于迭代器。

#### Tips：

List，Set，Map将持有对象一律视为Object型别。

Collection、List、Set、Map都是接口，不能实例化。  
    继承自它们的 ArrayList, Vector, HashTable, HashMap是具象class，这些才可被实例化。  
vector容器确切知道它所持有的对象隶属什么型别。vector不进行边界检查。

1. 如果涉及到堆栈，队列等操作，应该考虑用 List，对于需要快速插入，删除元素，应该使用 LinkedList，如果需要快速随机访问元素，应该使用 ArrayList。
2. 如果程序在单线程环境中，或者访问仅仅在一个线程中进行，考虑非同步的类，其效率较高，如果多个线程可能同时操作一个类，应该使用同步的类。
3. 在除需要排序时使用 TreeSet、TreeMap 外，都应该使用 HashSet、HashMap，因为他们的效率高
4. 要特别注意对 hash 表的操作，key 对象要正确复写 equals 和 hashCode 。
5. 容器类仅能持有对象引用（指向对象的指针），而不是将对象信息 copy 一份至数列某位置。一旦将对象置入容器内，便损失了该对象的类别信息。
6. 尽量返回接口而非实际的类型，这就是针对抽象编程。

Attention

1. Collection 没有 get 方法来获取某个元素。只能通过 Iterator 遍历元素。
2. Set 和 Collection 拥有一模一样的接口。
3. List 可以通过 get 来一次取出一个元素。
4. 一般使用 ArrayList 。用 LinkedList 构造堆栈 stack、队列 queue。
5. Map 用 put\(k,v\)/get（k

 注意：1、Collection没有get\(\)方法来取得某个元素。只能通过iterator\(\)遍历元素。2、Set和Collection拥有一模一样的接口。3、List，可以通过get\(\)方法来一次取出一个元素。使用数字来选择一堆对象中的一个，get\(0\)...。\(add/get\)4、一般使用ArrayList。用LinkedList构造堆栈stack、队列queue。5、Map用 put\(k,v\) / get\(k\)，还可以使用containsKey\(\)/containsValue\(\)来检查其中是否含有某个key/value。      HashMap会利用对象的hashCode来快速找到key。6、Map中元素，可以将key序列、value序列单独抽取出来。使用keySet\(\)抽取key序列，将map中的所有keys生成一个Set。使用values\(\)抽取value序列，将map中的所有values生成一个Collection。为什么一个生成Set，一个生成Collection？那是因为，key总是独一无二的，value允许重复

