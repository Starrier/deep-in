# Collections.synchronized

```java
@ThreadSafe
Class collectionsExample<E>{
    public List<E> list =Collections.synchronizedList(new ArrayList<E>));
    
    public boolean putIfAbsent(E object){
         synchronized(list){
             boolean absent =!list.contains(object);
             if(absent){
                 list.add(object);
             }
             return absent;
         }
     }
}

```

