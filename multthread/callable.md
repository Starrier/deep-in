# Callable

 Callable接口定义了方法**public T call\(\) throws Exception**。我们可以在Callable实现中声明强类型的返回值，甚至是抛出异常。尽管在Executors类中已经有一些方法可以将Runnable对象转换为Callable对象，你最好还是仔细复审现有的Runnable实现或Thread的子类。为什么还要这样做？主要是为了检查和清除因为Runnable无法抛出检查型异常而采用的变通方案。同时，你可能希望利用call\(\)方法直接返回结果的能力，以省去读取值时的类型转换。

