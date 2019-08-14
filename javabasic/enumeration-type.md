---
title: Enumeration Type
date: 2019-02-15 23:28:59
tags: Java
index_img: ../post_img/enumeration-type.jpeg
---

## enumeration-type

JDK 1.5 引入的新类型 —— 枚举

### **应用场景**：

#### 1.常量

在 JDK 1.5 之前，定义常量方式为 `public static final`，现在，我们可以使用枚举来定义，而且枚举提供了比常量更多的方法。

``` Java
public enmu Color{
    RED,GREEN,BLANK,YELLOW
}
```

#### 2. Switch

JDK 1.6 之前，switch 语句只支持 int，char，enmu 类型，使用枚举，可以让代码的可读性更强。

``` Java
public enmu Color{
    RED,YELLOW,Bule
}

public class Test{
    Color color = Color.RED;

    public void change(){
        switch(color){
            case RED:
              color  = Color.YELLOW;
              break;
            case YELLOW:
              color = Color.BLUE;
              break;
            case BLUE:
              color = Color.RED;
              break;
        }
    }
}
```

#### 3. 向枚举中添加新方法：

如果想要自定义方法，那么枚举后必须添加一个分号。而且 Java 要求必须先定义 enmu 示例。

``` Java
 @Data
 public enmu Color{
     READ("red",1),GREEN("green",2);
     private String colorName;
     private int index;

     private Color(String colorName,int index){
         this.colorName=colorName;
         this.index=index;
     }

     public static String getColorName(int index){
         for(Color c: Color.values()){
             if(c.getIndex()==index){
                 return c.colorName;
             }
         }
         return null;
     }
 }
```

#### 4. 覆盖枚举方法

覆盖 `toString()` 方法：

``` Java 
public enmu Color{
     READ("red",1),GREEN("green",2);
     private String colorName;
     private int index;

     private Color(String colorName,int index){
         this.colorName=colorName;
         this.index=index;
     }
     @Override
     public String toString(){
         return this.index+"-"+this.colorName;
     }
}
```

#### 实现接口

所有的枚举都继承自 java.lang.Enum 类。由于 Java 不支持多继承，所以枚举对象不能再继承其它类。

``` Java
 public interface Behaviour {  
    void print();  
    String getInfo();  
}  
public enum Color implements Behaviour{  
    RED("红色", 1), GREEN("绿色", 2), BLANK("白色", 3), YELLO("黄色", 4);  
    // 成员变量  
    private String name;  
    private int index;  
    // 构造方法  
    private Color(String name, int index) {  
        this.name = name;  
        this.index = index;  
    }  
    //接口方法  
    @Override  
    public String getInfo() {  
        return this.name;  
    }  
    //接口方法  
    @Override  
    public void print() {  
        System.out.println(this.index+":"+this.name);  
    }  
}  
```

#### 6. 使用接口组织枚举

``` Java
public interface A{
    enum B implements A{
        a1,a2
    }
    enum C implements A{
        c1,c2
    }
}
```

#### 7 .枚举结合的使用

java.util.EnmuSet 和 java.util.EnumMap  是两个枚举集合。EnumSet 保证结合中的元素不重复；EnumMap 中的 key 是 enum 类型，而 value 则可以是任意类型。

