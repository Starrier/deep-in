---
title: java-8-collection
date: 2019-03-11 23:14:00
tags: java8
---

## 收集器

### 转换成其他集合

```java
stream.collect(toCollection(TreeSet::new))
```
