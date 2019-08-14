---
title: shell-2
date: 2019-02-15 19:19:05
tags: Linux
index_img: ../post_img/shell2.jpeg
---

## Shell

### Shell 传递参数

在执行 shell 脚本时，可以向脚本传递参数，脚本内获取参数的格式为：$n。n 代表一个数字，表示是第几个参数

``` Shell
#!/bin/bash
echo "variable1 : $0"
```

| 参数处理 | 说明|
|----|---|
| $# | 传递到脚本中的参数个数|
| $* | 以一个单字符串显示所有向脚本传递的参数|
| $$ | 脚本运行的当前进行 ID 号|
| $! | 后台运行的最后一个进程的 ID 号|
| $@ | 与 $* 相同，但使用时要加引号，并在引号中返回每个参数 |
| $_ | 显示 Shell 使用的当前选项，与 "$1" "$2" ... "$n" 的形式输出所有参数 |
| $? | 显示最后命令的退出状态。0 表示没有错误，其他任何值表明有错误。|

``` shell
#!/bin/bash
echo "Shell 传递参数实例！";
echo "第一个参数为：$1";
echo "参数个数为：$#";
echo "传递的参数作为一个字符串显示：$*";
```

执行脚本：

$ chmod +x test.sh
$ ./test.sh 1 2 3
Shell 传递实参实例！
第一个参数为：1
参数个数为：3
传递的参数作为一个字符串显示：1 2 3

$* 和 $@ 的区别：

相同点：都是引用所有参数
不同点：只有在双引号体现出来。假设在脚本运行时写了三个参数 1 2 3 ，则 “ * ” 等价于 1 2 3（传递了一个参数），而“@” 等价于 “1” “2” “3”（传递了三个参数）。

``` Shell
#！/bin/bash
echo "---\$* 演示 ---"
for i in "$": do
  echo $i
done

echo "---\$@ 演示 ---"
for i in "$@": do
  echo $i
done  
```

执行脚本，输出结果如下所示

$ chmod +x test.sh
$ ./test.sh 1 2 3
--- $* 演示 ---
1 2 3
--- $@ ---
1
2
3

### Shell 流程控制

#### if esle

##### **if**

``` shell
if condition
then
   command1
   command2
   command3
   ...
   commandN
fi
# 写成一行（适用于终端命令提示符）：
if[ $(ps -ef| grep -c "ssh") -gt 1];then echo "true";fi
```

末尾的 fi 就是 if 倒过来拼写，后面还有类似内容。

##### **if else**

``` shell
if condition
then
   command1
   command2
   ...
   commandN
else
   command
fi
```

##### if else - if else

``` shell
if condition
then
  command1
else condition
then
  command2
else
  command
fi
```

##### for 循环

``` Shell
for var in item1 item2 ... itemN
do
   command
done
# 写成一行
for var in item1 item2; do command done;
```

##### while 语句

while 语句用于不断执行一系列命令，也用于从输入文件中读取书籍；命令通常为测试条件。格式为：

``` shell
while condition
do
   command
done
```

以下是一个基本的 while 循环，测试条件为：如果 int <= 5， 那么条件返回真。

```shell
#!/bin/bash
int=1
while($int<=5)
do
  echo $int
  let "int++"
done
```

结果为：

>1
2
3
4
5