---
title: equals
date: 2019-02-13 22:20:06
tags: DataStruct
index_img: ../post_img/taoyifenxi.jpeg
---

### 重写 equals 时需要重写 hashCode

``` Java
 @Data
 @AllConstructors
 class Test{
     private static class Person{
         int id;
         String name;

      @Override
      public boolean equals(Object object){
          if(this == object){
              return true;
          }
          if(object == null || getClass() != object.getClass()){
              return false;
          }
          Person person = (Person)o;
          // 两个对象是否等值，通过 id 来确定
          return this.id == person.id;
      }
     }

     public static void main(String [] args){
         HashMap<Person,String> map = new HashMap<>();

         Person person = new Person(1,"test");
         map.put(person,"Hello");
         // 执行 get() 时，应该可以获取 hello，但结果为 null
         System.out.println("Result" + map.get(new Person(1,"world")));//null
     }
 }
```

尽管在 get 和 put 时，使用的 key 从逻辑上说是相等的（用 equals 方法比较），但由于没有重写 hashCode 方法，所以 put 操作时， key(hashCode) --> hash --> indexFor --> 最终的索引位置，而通过 key 取出 value 时， key(hashCode1) --> hash --> indexFor -- 最终位置，由于 hashCode 和 hashCode1 不相等，导致没有定位到同一个数组位置而返回逻辑上错误的值 null（也有可能碰巧定位到一个数组位置，但也会判断其 entry 的 hash 值是否相等，上面的 get 有说明）。
