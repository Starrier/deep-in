---
description: When   to  use  it
---

# if \(log.isDebugEnabled\(\)\) {}

### Log Evel

```text
LOG、DEBUG、INFO、WARN、ERROR、fatal
```

### Eg.

```java
if(LOGGER.isDebugEnabled()){
    LOGGER.debug(message);
    }
```

### isDebugEnabled\(\)

```java
public boolean isDebugEnabled(){
     if(repository.isDisabled(Level.DEBUG_INT)){
            return false;
     }
     return Level.DEBUG.isGreaterOrEual(this.getEffectiveLevel());
 }
```

```java
public void debug(){
    if(repository.isDisabled(Level.DEBUG_INT)){
        return ;
    }
    if(Level.DEBUG.isGreaterOrEqual(this.getEffectiveLevel())){
        forcedLog(FQCN,Level.DEBUG,message,null):
    }
}
```

#### conclusion

debug\(\)  和 isDebugEnabled\(\) 做了几乎一样的判断

*  用 isDebugEnabled 方法判断下是能提升性能的
*  直接使用logger.info\("User " + userId + " is using app " + appId\)来输出log，也能够达到log级别为INFO或在INFO以下时才输出：\("User " + userId + " is using app " + appId\)，因为logger.info方法内部有判断输出级别的代码。但是在进入logger.info函数之前，\("User " + userId + " is using app " + appId\) 这个表达式已经通过运算拼接成了一个字符串；而如果事先使用 if \(logger.isInfoEnabled\(\)\)进行判断，那么当log级别在INFO以上时，就能省去上述的字符串操作，在高并发和复杂log信息拼接的情况下，使用这种标准的方法输出log能够省去不小的系统开销。另外，如果构造log信息的过程需要大量字符串操作，建议使用StringBuilder来完成字符串拼接。
*  ERROR及其以上级别的log信息是一定会被输出的，所以只有logger.isDebugEnabled和logger.isInfoEnabled方法，而没有logger.isErrorEnabled方法。

