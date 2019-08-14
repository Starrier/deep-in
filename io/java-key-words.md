---
title: java-key-words
date: 2019-02-17 23:16:07
tags: java
index_img: ../post_img/java-key-words.jpeg
---

## java-key-wrods

| 作用域 | 当前类 | 同一package |子孙类   |   其他package |
| --- | --- | --- | --- | --|  
|public |   √    |    √     |     √      |    √ |
|protected |  √   |     √    |      √    |   ×|
|friendly  |  √   |  √    |   ×  |      ×   |
|private  | √   |   ×     |     ×    |    ×  |
没有时默认为friendly，如构造函数等~



abstract
assert
boolean
break
byte
case
catch
char
class
const
continue	default	do	double	else
enum	extends	final	finally	float
for	goto	if	implements	import
instanceof	int	interface	long	native
new	package	private	protected	public
return	strictfp	short	static	super
switch	synchronized	this	throw	throws
transient	try	void	volatile	while


### super

 1. 调用父类中被重写的方法
 2. 调用父类的构造函数
 3. 访问父类被隐藏的非私有成员变量
