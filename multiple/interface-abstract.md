---
title: interface-abstract
date: 2019-02-17 23:03:24
tags: java
index_img: ../post_img/interface-abstract.jpeg
---

## Interface & Abstract

如果子类构造器没有显示地调用超类的构造器，则将自动地调用超类默认（没有参数）的构造器。如果超类没有不带参数的构造器，并且在子类的构造器中有没有显示地调用超类的其他构造器，则Java编译器将报告错误。使用super调用构造器的语句必须是子类构造器的第一条语句