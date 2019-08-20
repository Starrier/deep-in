---
title: Collections
date: 2019-02-19 20:23:12
tags: Java
index_img: ../post_img/collections.jpeg
---

## collections

collection 是 Java 派生的主要集合类的两个之一，另一个是 map接口，二是 collection 是 Java util 包中的一个接口，它提供了对集合对象进行基本操作的通用接口方法，直接继承接口的有 list 和 set 集合。

提供了一系列内部类集合，主要有：

 1. 不可变集合
 2. 同步集合
 3. 有类型检查的集合
 4. 空集合
 5. 只含有一个元素的集合

还提供了一系列很有用的静态方法，主要包括以下功能：

 1. 排序
 2. 二分查找
 3. 反转
 4. 打乱
 5. 交换元素位置
 6. 复制
 7. 求出集合中最大/小值

### 1. 不可变集合

有时候，我们拿到一部分书籍，这个数据不允许修改、删除，只允许读取，我们可以使用 `Collections.unmodifiableXXX()` 方法来实现。`Collections` 提供了对以下集合的不可变支持：

`Collections.unmodifiableCollection(c)` 方法可以返回一个容器的包装类，这个包装类的添加、替换、删除操作都会抛出异常 `UnsupportedOperationException`。

```java
public static <T> Collection<T> unmodifiableCollection(Collection<? extends T> c) {
    return new UnmodifiableCollection<>(c);
}

//一个不可变的集合
static class UnmodifiableCollection<E> implements Collection<E>, Serializable {
    private static final long serialVersionUID = 1820017752578914078L;

    final Collection<? extends E> c;

    UnmodifiableCollection(Collection<? extends E> c) {
        if (c==null)
            throw new NullPointerException();
        this.c = c;
    }

    //普通查询操作都是支持的，没什么特别
    public int size()                   {return c.size();}
    public boolean isEmpty()            {return c.isEmpty();}
    public boolean contains(Object o)   {return c.contains(o);}
    public Object[] toArray()           {return c.toArray();}
    public <T> T[] toArray(T[] a)       {return c.toArray(a);}
    public String toString()            {return c.toString();}

    public Iterator<E> iterator() {
        return new Iterator<E>() {
            private final Iterator<? extends E> i = c.iterator();

            public boolean hasNext() {return i.hasNext();}
            public E next()          {return i.next();}
            public void remove() {    //迭代器的 remove 也不支持
                throw new UnsupportedOperationException();
            }
            @Override
            public void forEachRemaining(Consumer<? super E> action) {
                // Use backing collection version
                i.forEachRemaining(action);
            }
        };
    }

    //这里开始一系列修改操作就不支持了
    public boolean add(E e) {
        throw new UnsupportedOperationException();
    }
    public boolean remove(Object o) {
        throw new UnsupportedOperationException();
    }

    public boolean containsAll(Collection<?> coll) {
        return c.containsAll(coll);
    }
    public boolean addAll(Collection<? extends E> coll) {
        throw new UnsupportedOperationException();
    }
    public boolean removeAll(Collection<?> coll) {
        throw new UnsupportedOperationException();
    }
    public boolean retainAll(Collection<?> coll) {
        throw new UnsupportedOperationException();
    }
    public void clear() {
        throw new UnsupportedOperationException();
    }

    // Override default methods in Collection
    @Override
    public void forEach(Consumer<? super E> action) {
        c.forEach(action);
    }

    @Override
    public boolean removeIf(Predicate<? super E> filter) {
        throw new UnsupportedOperationException();
    }
}
```

### 2. 同步集合

如果需要将一个集合的所有操作都设置为线程安全的，`Collections.sychronizedXXX()`。

Collections 提供对以下集合的同步化支持：

 1. SynchronizedCollection
 2. SynchronizedSet
 3. SynchronizedSortedSet
 4. SynchronizedList
 5. SynchronizedRandomAccessList
 6. SynchronizedMap
 7. SynchronizedSortedMap

```java
public static <T> Collection<T> synchronizedCollection(Collection<T> c) {
    return new SynchronizedCollection<>(c);
}

//第二个参数是用于同步的对象
static <T> Collection<T> synchronizedCollection(Collection<T> c, Object mutex) {
    return new SynchronizedCollection<>(c, mutex);
}

static class SynchronizedCollection<E> implements Collection<E>, Serializable {
    private static final long serialVersionUID = 3053995032091335093L;

    final Collection<E> c;  // 实际的集合
    final Object mutex;     // 同步的对象

    SynchronizedCollection(Collection<E> c) {
        this.c = Objects.requireNonNull(c);
        mutex = this;
    }

    SynchronizedCollection(Collection<E> c, Object mutex) {
        this.c = Objects.requireNonNull(c);
        this.mutex = Objects.requireNonNull(mutex);
    }

    public int size() {
        synchronized (mutex) {return c.size();}
    }
    public boolean isEmpty() {
        synchronized (mutex) {return c.isEmpty();}
    }
    public boolean contains(Object o) {
        synchronized (mutex) {return c.contains(o);}
    }
    public Object[] toArray() {
        synchronized (mutex) {return c.toArray();}
    }
    public <T> T[] toArray(T[] a) {
        synchronized (mutex) {return c.toArray(a);}
    }

    public Iterator<E> iterator() {
        return c.iterator(); // 迭代器没有进行同步操作，需要使用者自己同步
    }

    public boolean add(E e) {
        synchronized (mutex) {return c.add(e);}
    }
    public boolean remove(Object o) {
        synchronized (mutex) {return c.remove(o);}
    }

    public boolean containsAll(Collection<?> coll) {
        synchronized (mutex) {return c.containsAll(coll);}
    }
    public boolean addAll(Collection<? extends E> coll) {
        synchronized (mutex) {return c.addAll(coll);}
    }
    public boolean removeAll(Collection<?> coll) {
        synchronized (mutex) {return c.removeAll(coll);}
    }
    public boolean retainAll(Collection<?> coll) {
        synchronized (mutex) {return c.retainAll(coll);}
    }
    public void clear() {
        synchronized (mutex) {c.clear();}
    }
    public String toString() {
        synchronized (mutex) {return c.toString();}
    }
    // Override default methods in Collection
    @Override
    public void forEach(Consumer<? super E> consumer) {
        synchronized (mutex) {c.forEach(consumer);}
    }
    @Override
    public boolean removeIf(Predicate<? super E> filter) {
        synchronized (mutex) {return c.removeIf(filter);}
    }
    @Override
    public Spliterator<E> spliterator() {
        return c.spliterator(); // Must be manually synched by user!
    }
    @Override
    public Stream<E> stream() {
        return c.stream(); // Must be manually synched by user!
    }
    @Override
    public Stream<E> parallelStream() {
        return c.parallelStream(); // Must be manually synched by user!
    }
    private void writeObject(ObjectOutputStream s) throws IOException {
        synchronized (mutex) {s.defaultWriteObject();}
    }
}
```

可以发现，它只是在所有方法上都添加了 `synchronized`，这样得到的集合在并发环境中效率可能会大打折扣。

在使用 `Collections.synchronizedCollection(c)` 返回迭代器时，需要手动对迭代器进行同步，否则在迭代时有并发操作，可能会导致不确定性问题。其他同步集合都类似，使用 `synchronized`。

```java
Collection c = Collections.synchronizedCollection(myCollection);
...
 synchronized(c){
     Iterator i = c.iterator();
     while(i.hasNext()){
         foo(i.next());
     }
 }
```