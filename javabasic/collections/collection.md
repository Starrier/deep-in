# Collection

 1. 集合可以存储多个元素，但我们对多个元素也有不同的需求：多个元素，不能有相同的；多个元素能够按照某个规则排序
 2. 针对不同的需求，Java 能提供很多集合类，多个集合类的数据结构不同。但结构不重要，重要的是能够存储东西，能判断、获取。
 3. 把集合共性的内容不断往上提取，最终形成集合的继承体系 —— Collection。

## List

实现类：ArrayList、LinkedList、Vector

List 中对象按照索引位置排序，可以有重复对象，允许按照对象在集合中的索引位置检索对象，如通过 list.get(i) 方法来获取 List 集合中的元素。**以特定次序存储元素，所以取出来的顺序和放入的属性可能有所不同。**

## Set

实现类：HashSet、TreeSet

Set 中对象不按特定方式排序，没有重复对象。但他有实现类能对集合中的对象按特定方式排序，如 TreeSet 类，它可以按照默认排序，也可以通过实现 Java.util.Comparator<Type> 接口来自定义排序方式。Set 是无序的集合接口。

HashSet 底层是 HashMap，他有着 HashMap 键特性 —— 无序，不可重复性。

## Map 

实现类：HashMap、TreeMap、HashTable

Map 中的每个元素都包含一个键对象和值对象，它们成对出现。键对象不能重复，但是值对象可以。

### 数组和集合的区别：

 1. 长度区别：数组的长度固定;集合的长度可变
 2. 内容不同：数组存储的是同一种类型的元素；集合可以存储不同类型的元素
 3. 元素的数据类型：数组可以存储基本数据类型，也可以存储引用类型；集合只能存储引用类型（你存储的是简单的 int，它会自动装箱成 Integer）
