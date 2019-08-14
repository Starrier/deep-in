---
title: Collections-2
date: 2019-02-20 20:47:29
tags: DataStruct
index_img: ../post_img/Collections2.jpeg
---

## Collections-2

### 3. 有类型检查的集合

日常开发中，会涉及到返回值为 Object，然后再根据具体的使用进行强制类型转换，在这个强转的过程，编译器无法检查出强转是否成功。

这时，我们可以使用 `Collections.checkedXXX(c,type)` 方法创建一个只保存某个类型数据的集合，在添加时会进行类型检查操作。**虽然使用泛型也可以实现在编译时检查，但在运行时，泛型檫除成 Objcet，最终还是需要进行强制类型转换**。

Collections 提供对以下集合的类型检查

 1. CheckedCollection
 2. CheckedSet
 3. CheckedSortedSet
 4. CheckedList
 5. CheckedRandomAccessList
 6. CheckedMap
 7. CheckedSortedMap

`Collections.checkedCollection(c,type)` 方法提供了

```Java
public static <E> Collection<E> checkedCollection(Collection<E> c,
                                                  Class<E> type) {
    return new CheckedCollection<>(c, type);
}

@SuppressWarnings("unchecked")
static <T> T[] zeroLengthArray(Class<T> type) {
    return (T[]) Array.newInstance(type, 0);
}

/**
 * @serial include
 */
static class CheckedCollection<E> implements Collection<E>, Serializable {
    private static final long serialVersionUID = 1578914078182001775L;

    final Collection<E> c;
    final Class<E> type;    //目标类型

    void typeCheck(Object o) {
        if (o != null && !type.isInstance(o))    //检查是否和目标类型一致
            throw new ClassCastException(badElementMsg(o));
    }

    private String badElementMsg(Object o) {
        return "Attempt to insert " + o.getClass() +
            " element into collection with element type " + type;
    }

    CheckedCollection(Collection<E> c, Class<E> type) {
        if (c==null || type == null)
            throw new NullPointerException();
        this.c = c;
        this.type = type;
    }

    public int size()                 { return c.size(); }
    public boolean isEmpty()          { return c.isEmpty(); }
    public boolean contains(Object o) { return c.contains(o); }
    public Object[] toArray()         { return c.toArray(); }
    public <T> T[] toArray(T[] a)     { return c.toArray(a); }
    public String toString()          { return c.toString(); }
    public boolean remove(Object o)   { return c.remove(o); }
    public void clear()               {        c.clear(); }

    public boolean containsAll(Collection<?> coll) {
        return c.containsAll(coll);
    }
    public boolean removeAll(Collection<?> coll) {
        return c.removeAll(coll);
    }
    public boolean retainAll(Collection<?> coll) {
        return c.retainAll(coll);
    }

    public Iterator<E> iterator() {
        // JDK-6363904 - unwrapped iterator could be typecast to
        // ListIterator with unsafe set()
        final Iterator<E> it = c.iterator();
        return new Iterator<E>() {
            public boolean hasNext() { return it.hasNext(); }
            public E next()          { return it.next(); }
            public void remove()     {        it.remove(); }};
        // Android-note: Should we delegate to it for forEachRemaining ?
    }

    public boolean add(E e) {    //添加时调用了类型检查
        typeCheck(e);
        return c.add(e);
    }

    private E[] zeroLengthElementArray = null; // Lazily initialized

    private E[] zeroLengthElementArray() {
        return zeroLengthElementArray != null ? zeroLengthElementArray :
            (zeroLengthElementArray = zeroLengthArray(type));
    }

    public boolean addAll(Collection<? extends E> coll) {
        //这个方法可以让我们避免并发时内容改变导致的问题
        return c.addAll(checkedCopyOf(coll));
    }

    @SuppressWarnings("unchecked")
    //检查集合中的每个元素的类型
    Collection<E> checkedCopyOf(Collection<? extends E> coll) {
        Object[] a = null;
        try {
            E[] z = zeroLengthElementArray();
            a = coll.toArray(z);
            // Defend against coll violating the toArray contract
            if (a.getClass() != z.getClass())
                a = Arrays.copyOf(a, a.length, z.getClass());
        } catch (ArrayStoreException ignore) {
            // To get better and consistent diagnostics,
            // we call typeCheck explicitly on each element.
            // We call clone() to defend against coll retaining a
            // reference to the returned array and storing a bad
            // element into it after it has been type checked.
            //
            a = coll.toArray().clone();
            for (Object o : a)
                typeCheck(o);
        }
        // A slight abuse of the type system, but safe here.
        return (Collection<E>) Arrays.asList(a);
    }

    // Override default methods in Collection
    @Override
    public void forEach(Consumer<? super E> action) {c.forEach(action);}
    @Override
    public boolean removeIf(Predicate<? super E> filter) {
        return c.removeIf(filter);
    }
    @Override
    public Spliterator<E> spliterator() {return c.spliterator();}
    @Override
    public Stream<E> stream()           {return c.stream();}
    @Override
    public Stream<E> parallelStream()   {return c.parallelStream();}
```

可以看到，只是在 `add(e)`、`addAll()` 时进行了类型检查而已，不符合目标类型就会抛出 `ClassCastException` 异常。

### 4. 空集合

### 5. 只含有一个元素的集合

有时，第三方方法需要的参数是一个 Map，而我们只有一组 K、V 数据，可以使用 `Collections.singletonMap(key,value)` 创建一个 Map。

```java
public static <K,V> Map<K,V> singletonMap(K key, V value) {
    return new SingletonMap<>(key, value);
}

private static class SingletonMap<K,V>
      extends AbstractMap<K,V>
      implements Serializable {
    private static final long serialVersionUID = -6979724477215052911L;

    private final K k;
    private final V v;

    //构造函数中将参数的 k-v 保存到唯一的 k 和 v 中
    SingletonMap(K key, V value) {
        k = key;
        v = value;
    }

    public int size()                          {return 1;}

    public boolean isEmpty()                   {return false;}

    public boolean containsKey(Object key)     {return eq(key, k);}

    public boolean containsValue(Object value) {return eq(value, v);}

    public V get(Object key)                   {return (eq(key, k) ? v : null);}

    private transient Set<K> keySet = null;
    private transient Set<Map.Entry<K,V>> entrySet = null;
    private transient Collection<V> values = null;

    public Set<K> keySet() {
        if (keySet==null)
            keySet = singleton(k);
        return keySet;
    }

    public Set<Map.Entry<K,V>> entrySet() {
        if (entrySet==null)
            entrySet = Collections.<Map.Entry<K,V>>singleton(
                new SimpleImmutableEntry<>(k, v));
        return entrySet;
    }

    public Collection<V> values() {
        if (values==null)
            values = singleton(v);
        return values;
    }

    // Override default methods in Map
    @Override
    public V getOrDefault(Object key, V defaultValue) {
        return eq(key, k) ? v : defaultValue;
    }

    @Override
    public void forEach(BiConsumer<? super K, ? super V> action) {
        action.accept(k, v);
    }

    @Override
    public void replaceAll(BiFunction<? super K, ? super V, ? extends V> function) {
        throw new UnsupportedOperationException();
    }

    @Override
    public V putIfAbsent(K key, V value) {
        throw new UnsupportedOperationException();
    }

    @Override
    public boolean remove(Object key, Object value) {
        throw new UnsupportedOperationException();
    }

    @Override
    public boolean replace(K key, V oldValue, V newValue) {
        throw new UnsupportedOperationException();
    }

    @Override
    public V replace(K key, V value) {
        throw new UnsupportedOperationException();
    }

    @Override
    public V computeIfAbsent(K key,
                             Function<? super K, ? extends V> mappingFunction) {
        throw new UnsupportedOperationException();
    }

    @Override
    public V computeIfPresent(K key,
                              BiFunction<? super K, ? super V, ? extends V> remappingFunction) {
        throw new UnsupportedOperationException();
    }

    @Override
    public V compute(K key,
                     BiFunction<? super K, ? super V, ? extends V> remappingFunction) {
        throw new UnsupportedOperationException();
    }

    @Override
    public V merge(K key, V value,
                   BiFunction<? super V, ? super V, ? extends V> remappingFunction) {
        throw new UnsupportedOperationException();
    }
}
```

**注意：**返回的 map 是不支持删除和替换操作的。
