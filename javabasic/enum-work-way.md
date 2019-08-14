---
title: enum-work-way
date: 2019-02-16 01:29:39
tags: DataStruct
index_img: ../post_img/enum.jpeg
---

## 实现的原理

使用 `enum` 关键字穿件枚举类并编译后，编译器会为我们生成一个相关的 class，这个 class 继承了 java.lang.Enum，也就是说通过关键字 `enum` 创建枚举类型后，编译生成的是类类型，且继承 java.lang.Enum 类。

``` java
public enum ColorEnum {
    RED("red", 1),
    GREEN("green", 2);
}
```

反编译后：

```Java
 final class ColorEnum extends Enum{
     public static ColorEnum [] values(){
         return (ColorEnum[])$VALUES.clone();
     }

     public static ColorEnum valueOf(String s){
         return (ColorEnum).valueOf(包名/ColorEnum,s);
     }

     private ColorEnum(String s,int i){
         super(s,i);
     }

     public static final ColorEnum RED;
     public static final ColorEnum GREEN:
     private static final ColorEnum $VALUES[];

     static {
         RED =new ColorEnum("RED",0);
         GREEN =new COlorEnum("GREEN",1);

         $VALUES=(new ColorEnum []{
             RED;GREEN
         });
     }
 }
```

由此可知：

1. 编译时会生成一个继承 java.lang.Enum 的类，该类是 `final` 类型的，无法被继承。
2. 生成了 ColorEnum 类型的实例对象，对应 ColorEnum 中的枚举内容。
3. 生成两个静态方法
4. 编译后，会生成一个类，类中会存在枚举类型定义好的变量的对应实例对象。

#### Enum 抽象类常见方法

| 返回类型 | 方法名称 | 说明 |
| ------ | ------ | ------ |
| int | comparaTo(E o) |  比较此枚举与指定对象的顺序 |
| boolean | equals(Object other) | 当指定对象等于此枚举常量时，返回 true |
|Class<?>|getDeclaringClass()|返回与此枚举常量的枚举类型相对应的 Class 对象|
|String| name()| 返回此美景常量的名称，在其枚举声明中对其进行声明|
