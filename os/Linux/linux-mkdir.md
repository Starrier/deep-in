---
title: linux-mkdir
date: 2019-03-12 23:24:27
tags: linux
index_img: ../post_img/jvm-oom.jpeg
---

Linux 常用命令

一 . mkdir

 mkdir 命令用于创建指定名称的目录，要求创建目录的用户在当前目录中有写权限，并且指定的目录名不能是当前目录中已有的目录。

 1. 格式：

 > mkdir[options] 目录

 2. 功能：

在指定位置创建文件名命名的文件夹或目录，要创建文件夹或目录的用户必须对所创建的文件夹的父文件夹具有写权限，且同一个目录下不能有重名的。

 3. 命令参数：

 -m ，-mode 模式，设定权限<模式>(类似 chmod)，而不是 rwxrwxrwx
 -p ，-parents 可以是一个路径的名称，此时若路劲中的某些目录不存在，加上此选项后系统将自动建立那些尚不存在的目录，即一次可以建立多个目录。
 -v ，-verbose 创建当前新目录或文件时，显示信息，如“已创建目录 test”

 4. example:

``` shell
# 创建一个空目录
mkdir test

# 递归创建多个目录
mkdir -p /test/test

# 创建权限为 777 的目录
mkdir -m 777 test 777

# 创建项目的目录结构
mkdir -vp
```

**最后一个实例，对顺序是有要求的，即在 -vp 时才表示 -v –p 操作，而若是 -pv 则在上述语句中会默认为创-vp 目录**。
