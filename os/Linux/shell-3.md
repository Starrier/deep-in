---
title: shell-3
date: 2019-02-17 12:08:29
tags: Linux
index_img: ../post_img/shell3.jpeg
---

## shell 函数

Linux shell 可以自定义函数，在 shell 脚本中调用。格式为：

```shell
[ function ] funname [()]
{
    action;
    [return int;]
}
```

**注意**：

 1. 可以带 function fun() 定义，也可以直接 fun() 定义，不带任何参数
 2. 参数返回，可以显式加 return 返回，如果不加，将以最后一条命令运行结果，作为返回值。return 后跟数值 n（0-255）

``` shell
#!/bin/bash
testFun(){
  echo "hello"
}
testFun  # 输出 hello
```

带有 return 的语句

``` shell
#!/bin/bash
testFun{
    echo "请输入第一个数字："
    read a
    echo "请输入第二个数字："
    read b
    echo "两个数字分别为 $a 和 $b"
    return $(($a + $b))
}
testFun
echo "结果为 $?"
```