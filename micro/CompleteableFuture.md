# CompletableFutrue

## Future

异步计算，返回一个执行运算结果的引用（回调函数），当运算结束后，这个引用被返回给调用方。将一个耗时的操作，封装在 Callable 对象中，再将它提交给 ExecutorService。