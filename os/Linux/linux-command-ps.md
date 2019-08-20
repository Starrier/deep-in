---
title: linux-command-ps
date: 2019-02-22 22:44:53
tags: Linux
index_img: ../post_img/linux-command-ps.jpeg
---

## Linux 进程相关的命令

### 查看进程的命令

### **ps**:

 1. 显示现行终端机下的所有程序，包括其他用户的程序：

```text
ps a
```

 2. 显示所有程序

 ```text
 ps A
 ```

 3. 列出程序时，显示每个程序真正的指令名称，不包括路径，参数或常驻服务的标注：

 ```text
 ps c
 ```

 4. 此参数效果和指定 “A” 参数相同

 ```text
 ps -e
 ``` 

 5. 列出程序时，显示每个程序所使用的环境变量

 ```text
 ps e
 ```

 6. 用 ASCII 字符显示树状结构，表达程序间的相互关系

 ```text
 ps f
 ```
 
 7. 显示树状结构，表示程序间的相互关系

 ```text
 ps -H
 ```

 8. 显示所有程序，除了执行 ps 指令终端机下的程序之外

 ```text
 ps -N
 ```

 9. 采用程序信号的格式显示程序状况

 ```text
 ps s
 ```

 10. 列出程序时，包括已中断的子程序资料

 ```text
 ps S
 ```

 11. 指定终端机号，并列出属于该终端机的程序的状况

 ```text
 ps t <终端机编号>
 ```
 
 12. 以用户为主的格式来显示程序状况

 ```text
 ps u
 ```

 13. 显示所有程序，不以终端机来区分。

 ```text
 ps X 
 ```

最常用的方法是 ps aux，然后再通过管道使用 grep 命令过滤查找特定的进程，然后再对特定的进程进行操作。

```text
 ps aux | grep program_filter_worl,ps -ef | grep tomcat
```

显示所有的 java 进程，去除当前的 grep 进程

```text
ps -ef |gerp java | grep -v grep
```