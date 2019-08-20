---
title: shell-get-start
date: 2019-02-15 11:01:32
tags: Linux
index_img: ../post_img/shell.jpeg
---

Shell 的开头必须以指定的格式开启：

``` shell
 #!/bin/bash
```

1. ./test.sh 执行当前目录下的 `1.sh` 文件（1.sh 必须是可执行文件才行）

2. bash  /user/local/1.sh

## shell 变量

### 定义变量名

1. 命名只能使用英文字母，数字和下划线，首个字母不能以数字开头
2. 中间不能有空格，可以使用下划线
3. 不能使用标点符号
4. 不能使用 bash 里的关键字（可用 help 查看保留的关键字）

**赋值时，`=` 左右不能有空格**。

### 使用变量名

使用一个定义过的变量，只要在变量名前面加上 `$` 符号即可（已定义的变量，可以重新赋值）：

``` shell
test="test"
echo $test
//推荐以下写法，帮助解释器识别变量边界
echo ${test}
```

### 删除变量

使用  `unset` 命令删除（**不能删除只读变量**）：

``` shell
unset test
```

### 变量类型

1. **局部变量**：局部变量在脚本或命令中定义，仅在当前 shell 实例中有效，其他 shell 启动的程序不能访问局部变量。
2. **环境变量**：所有程序，包括 shell 启动的程序，都可以访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候 shell 脚本也可以定义环境变量。
3. **shell 变量**:shell 变量是由 shell 程序设置的特殊变量。shell 变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了 shell 的正常运行

## shell 字符串

单引号，双引号都可以。

单引号：

```shell
str='string test'
```

单引号的限制：

1. 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的
2. 单引号字符串中不能出现单独一个单引号（对单引号使用转义符号也不行），但可以成对出现，作为字符串拼接使用

双引号：

``` shell
test="test"
str="Hello,\"$test"\!\n"

echo -e $str
```

输出结果：

``` shell
hello,test!
```

双引号的优点：

1. 双引号中可以有变量
2. 双引号中可以出现转义字符

**拼接字符串**：

``` shell
test="test"
# 双引号拼接
a="hello,"test" !"
a1="hello,${test}!"

echo $a $a1

# 单引号拼接
b='hello,'${test}''
b='hello,${test}'
```

**获取字符串长度**：

``` shell
string="abcd"
# 4
echo ${#string}
```

**提取字符串**：

```shell
string="hello"
# ello
echo ${string:1:4}
```

**查找子字符串**：

```shell
string="hello"
# 查找字符串 i 或者 o 的位置，哪个先出现，就计算哪个
echo 'expr index "$string" io'
```

### shell 数组

bash 支持一维数组（不支持多维数组），不限制数组的大小

**数组的定义**：用括号表示数组，数组元素用空格分隔开

``` shell
数组名=（值1 值2 ... 值n）

array_test=(1 2 3 4)

array_test(
    1
    2
    3
)

array_test[0]=1
array_test[1]=2 # 数组可以使用不连续的小标，且没有限制
```

**读取数组**：

``` shell
${数组名[下标]}

array1=${array_test[0]}

echo ${array_test[@]} # 使用 @ 符号可以读出所有的元素
```

**获取数组的长度**：

```shell
length=${#array_name[@]}

length=${array_test[*]}

length=${#array_test[n]}
```

`./` 与 `. ./` 都可以执行脚本，但是前者是在子进程中运行脚本，后者是在当前进程中执行脚本。非当前进程是看不到 aa 的。所以应该使用第二种。

``` shell
#!/bin/bash

aa="hello"

./test.sh >/dev/null 1 && echo $aa
与
. ./test.sh >/dev/null 1 && echo $aa


$ ./test.sh
$ . ./test.sh
```