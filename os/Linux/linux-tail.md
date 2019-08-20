---
title: linux-tail
date: 2019-03-12 23:24:16
tags: linux
index_img: ../post_img/jvm-oom.jpeg
---

# 查看日志的常用命令

## `tail`. `cat`, `tac`, `head`, `echo`

从第 3000 行开始，显示 1000 行，即显示 3000~3999 行

`cat filename | tail -n +3000 | head -n 1000`

显示 1000 行到 3000 行

`cat filename | head -n 3000 | tail -n +1000`

-tail

  -n 显示行号：相当于 nl 命令：

  tail -1000f test.log 实时监控 1000 行日志
  tail -n 10 test.log 查看日志尾部最后 10 行的日志
  tail -n +10 test.log 查看日志 10 行之后的所有日志

head

与 tail 命令相反，tail 是看到最后多少行日志：

 head -n 10 test.log 查询日志文件中的头 10 行日志
 head -n -10 test.log 查询日志文件除了最后 10 行的其他所有日志

### cat

使用 more 和 less 命令：

cat -n test.log | grep "debug" | more 可以分页查看，通过点击空格键翻页

使用 >xxx.txt 将其保存到文件中，到时可以拉下来这个文件分析
