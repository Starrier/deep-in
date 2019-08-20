# 有序集合/无序集合

有序集合：集合里的元素可以根据key或index访问

无序集合：集合里的元素只能遍历。有序集合在属性的增加，删除及修改中拥有较好的性能表现。

无序集： Set

有序集： List

  
所以，凡是实现set的AbstractSet, CopyOnWriteArraySet, EnumSet, HashSet, JobStateReasons, LinkedHashSet, TreeSet都是无序的

  
凡是实现List的AbstractList, AbstractSequentialList, ArrayList, AttributeList, CopyOnWriteArrayList, LinkedList, RoleList, RoleUnresolvedList, Stack, Vector

