# try catch finally

## **try里有return，finally还执行么？**

任然会执行。

1、不管有木有出现异常，finally块中代码都会执行；

  
2、当try和catch中有return时，finally仍然会执行；

  
3、finally是在return后面的表达式运算后执行的（此时并没有返回运算后的值，而是先把要返回的值保存起来，不管finally中的代码怎么样，返回的值都不会改变，任然是之前保存的值），所以函数返回值是在finally执行前确定的；

  
4、finally中最好不要包含return，否则程序会提前退出，返回值不是try或catch中保存的返回值。

