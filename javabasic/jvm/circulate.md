## Circulate

### for

for循环的执行顺序用如下表达式：

```Java
for(expression1;expression2;expression3){
expression4;
}
```

执行的顺序应该是：

1）第一次循环，即初始化循环。首先执行表达式expression1（一般为初始化语句）；再执行expression2（一般为条件判断语句），判断expression1是否符合expression2的条件；如果符合，则执行expression4，否则，停止执行；最后执行expression3。

2）第N（N>=2）次循环

首先执行expression2，判断在expression3是否符合在expression2要求；如果符合，则继续执行在expression4，否则，停止执行。最后执行在expression3。如此往复，直至expression3不满足在expression2条件是为止。

总结：

总的来说，执行的顺序是一致的。先条件判断（expression2），再函数体执行（expression4），最后for执行（expression3）。往复......区别在于，条件判断的对象。第一次判断时，对象为初始化语句（expression1），后续的判断对象为执行后的结果（expression3）。
