```java
    public void set(T value) {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null) {
            map.set(this, value);
        } else {
            createMap(t, value);
        }
    }
```



```java
/**
 * 获取与 ThreadLocal 关联的映射。
*  在 Inheritable ThreadLocal中 重写。
 */
 ThreadLocalMap getMap(Thread t) {
        return t.threadLocals;
    }
```