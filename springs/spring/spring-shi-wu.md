# Spring 事务

Spring 事务的隔离性

1.  @Transaction 只能应用在 public 方法上，对于非 public 方法，如果标记了 @Transaction，不报错，但方法没有事务功能。
2. Spring 事务管理器，由 Spring 负责数据库的打开，提交，回滚。默认遇到运行期例外（throw new RuntimeException\("注解"\)）会回滚，即遇到不受检查的例外（就是非运行时异常，编译器会检查到异常）
3. @Transaction 注解应该只被应用到 public 方法，如果在 protected、private 或 package-visiable 方法上，注解失效。
4. @Transaction 注解可以被应用于接口定义和接口方法，类定义和类的 public 方法上。然而，它不足以开启事务。
5. 应该在类上使用 @Transaction，不要在类所要实现的任何接口上。也可以在接口上使用，但这只能将当你







 而遇到需要捕获的例外\(throw new Exception\("注释"\);\)不会回滚,即遇到受检查的例外（就是非运行时抛出的异常，编译器会检查到的异常叫受检查例外或说受检查异常）时，需我们指定方式来让事务回滚 要想所有异常都回滚,要加上 @Transactional\( rollbackFor={Exception.class,其它异常}\) .如果让unchecked例外不回滚： @Transactional\(notRollbackFor=RunTimeException.class\)  
如下:  
@Transactional\(rollbackFor=Exception.class\) //指定回滚,遇到异常Exception时回滚  
public void methodName\(\) {  
throw new Exception\("注释"\);  
  
}  
@Transactional\(noRollbackFor=Exception.class\)//指定不回滚,遇到运行期例外\(throw new RuntimeException\("注释"\);\)会回滚  
public ItimDaoImpl getItemDaoImpl\(\) {  
throw new RuntimeException\("注释"\);  
}







