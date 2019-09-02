# ArrayList 和 Vector 的区别：

共同点：

这两个类都实现了List接口，它们都是有序的集合(存储有序)，底层是数组。我们可以按位置索引号取出某个元素，允许元素重复和为null。
区别：

同步性：
ArrayList是非同步的
Vector是同步的
即便需要同步的时候，我们可以使用Collections工具类来构建出同步的ArrayList而不用Vector
扩容大小：
Vector增长原来的一倍，ArrayList增长原来的0.5倍

Set里的元素是不能重复的，那么用什么方法来区分重复与否呢? 是用==还是equals()?
我们知道Set集合实际大都使用的是Map集合的put方法来添加元素。

以HashSet为例，HashSet里的元素不能重复，在源码(HashMap)是这样体现的：

	
	// 1. 如果key 相等  
    if (p.hash == hash &&
        ((k = p.key) == key || (key != null && key.equals(k))))
        e = p;
	// 2. 修改对应的value
	   if (e != null) { // existing mapping for key
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
       }
添加元素的时候，如果key(也对应的Set集合的元素)相等，那么则修改value值。而在Set集合中，value值仅仅是一个Object对象罢了(该对象对Set本身而言是无用的)。

也就是说：Set集合如果添加的元素相同时，是根本没有插入的(仅修改了一个无用的value值)！从源码(HashMap)中也看出来，==和equals()方法都有使用！

与Java集合框架相关的有哪些最好的实践
根据需要确定集合的类型。如果是单列的集合，我们考虑用Collection下的子接口ArrayList和Set。如果是映射，我们就考虑使用Map~
确定完我们的集合类型，我们接下来确定使用该集合类型下的哪个子类~我认为可以简单分成几个步骤：
是否需要同步
去找线程安全的集合类使用
迭代时是否需要有序(插入顺序有序)
去找Linked双向列表结构的
是否需要排序(自然顺序或者手动排序)
去找Tree红黑树类型的(JDK1.8)
估算存放集合的数据量有多大，无论是List还是Map，它们实现动态增长，都是有性能消耗的。在初始集合的时候给出一个合理的容量会减少动态增长时的消耗~
使用泛型，避免在运行时出现ClassCastException
尽可能使用Collections工具类，或者获取只读、同步或空的集合，而非编写自己的实现。它将会提供代码重用性，它有着更好的稳定性和可维护性
