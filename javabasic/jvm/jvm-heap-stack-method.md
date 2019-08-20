# JVM Heap Stack Method

### Heap:

1. 存储的全是对象，每个对象都包含一个与之对应的 Class 信息（class 的目的是得到操作的指令）
2. JVM 只有一个堆（heap）,被所有线程共享，堆中不存放基本类型和对象引用，只存放对象本身。

### Stack

1. 每个线程包含一个栈区，栈中只保存基础数据类型的对象和自定义对象的引用（不是对象），对象都存放在堆区中。
2. 每个栈中的数据（原始数据类型和对象引用）都是私有的，其他栈不能访问。
3. 栈分为 3 个部分：基本类型变量区、执行环境上下文、操作指令区（存放操作指令）。

### Method

1. 又叫静态区，跟堆一样，被所有线程共享。方法区包含所有 class 和 static 变量。
2. 方法区中包含的都是整个程序中永远唯一的元素，如 class、static 变量。

## Test

{% code-tabs %}
{% code-tabs-item title="AppMain.java" %}
```java
//运行时, jvm 把appmain的信息都放入方法区
public   class  AppMain{
    //main 方法本身放入方法区。 
    public   static   void  main(String[] args) {
       //test1是引用，所以放到栈区里， Sample是自定义对象应该放到堆里面
       Sample test1 = new  Sample( " 测试1 " );  
       Sample test2 = new  Sample( " 测试2 " );
       test1.printName();  
       test2.printName();
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

```java
//运行时, jvm 把appmain的信息都放入方法区
public   class  Sample{
/** 范例名称 */
private  name;
      //new Sample实例后， name 引用放入栈区里，  name 对象放入堆里

/** 构造方法 */
public  Sample(String name){
    this .name = name;
}

/** 输出 */
public   void  printName()   //print方法本身放入 方法区里。{
System.out.println(name);
}
} 
```

![](../../.gitbook/assets/jvmheapstatcmethodbmp.bmp)

