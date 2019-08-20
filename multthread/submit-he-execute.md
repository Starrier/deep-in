# submit 和 execute

## 区别

#### 一  . 接收的参数不一样;

#### 二  . submit\(\)有返回值，而execute\(\)没有;

　    例如，有个validation的task，希望该task执行完后告诉我它的执行结果，是成功还是失败，然后继续下面的操作。

#### 三 .submit\(\)可以进行Exception处理;

　    例如，如果task里会抛出checked或者unchecked exception，而你又希望外面的调用者能够感知这些exception并做出及时的处理，那么就需要用到submit，通过对Future.get\(\)进行抛出异常的捕获，然后对其进行处理。

## 分析

#### execute\(\) 是在Executor接口中定义的，ThreadPoolExecutor继承了AbstractExecutorService抽象类，该抽象类实现了ExecutorService接口（但并没有覆盖execute方法），而ExecutorService接口继承了Executor接口。

```java
submit public Future<？> submit(Runnable task)
```

提交一个 Runnable 任务用于执行，并返回一个表示该任务的 Future。该 Future 的 get 方法在成功 完成时将会返回null。

指定者： 接口 ExecutorService 中的 submit 参数： task - 要提交的任务 返回： 表示任务等待完成的 Future

```java
submit public Future submit(Runnable task,T result) 
```

提交一个 Runnable 任务用于执行，并返回一个表示该任务的 Future。该 Future 的 get 方法在成功完成时将会返回给定的结果。

指定者： 接口 ExecutorService 中的 submit 参数： task - 要提交的任务 result - 返回的结果 返回： 表示任务等待完成的 Future

```java
submit public Future submit(Callable task) 
```

提交一个返回值的任务用于执行，返回一个表示任务的未决结果的 Future。该 Future 的 get 方法在成功完成时将会返回该任务的结果。 如果想立即阻塞任务的等待，则可以使用 result = exec.submit\(aCallable\).get\(\); 形式的构造。

注：Executors 类包括了一组方法，可以转换某些其他常见的类似于闭包的对象，例如，将 PrivilegedAction 转换为Callable 形式，这样就可以提交它们了。

指定者： 接口 ExecutorService 中的 submit 参数： task - 要提交的任务 返回： 表示任务等待完成的Future

### 总结：

 一 .**如果提交的任务不需要一个结果的话直接用execute\(\)会提升很多性能**

#### 二 . 如果传的任务是需要结果的，那你就使用你的类去继承 Callable 接口，然后告诉 _****_submit 方法就行了，如果你只需要一个特定的结果，就把那个特定的结果告诉 submit 方法然后把你想要的特定结果也告诉他，它只是帮你完成以前使用 Future 模式的时候你自己需要做的那些步骤而已，如果你不需要一个结果，那么就老老实实使用execute，如果你需要的是一个空结果，那么 submit\(yourRunnable\) 与 submit\(yourRunnable,null\) 是等价的！

