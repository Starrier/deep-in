# @NotNull

实际上，@NotNull 注释是明确的声明了以下内容的协定：

* 一个方法不应该返回 null。
* 变量（如字段、局部变量和参数）不能保留 null 值。

是否如果违反了这些协定，IntelliJ IDEA 将警告您。



### @Nullable 和 @NotNull 注释正式的语义

用 @Nullable 声明注释的元素 null 值对于返回 \(方法\)、传递给 \(参数\) 和保持 \(对于局部变量和字段\) 是完全有效的。   


用 @NotNull 声明注释的元素不允许返回 null 值 （对于方法），传递给（对于参数）和保持（对于局部变量和字段）。

当使用带注释的声明或参数来重写/实现方法时，@Nullable 和 @NotNull 之间存在一个协方差-逆变关系。

* 使用注释声明重写/实现方法：父方法的 @NotNull 注释需要针对子类方法的 @NotNull 注释。使用父方法中的 @Nullable 注释的方法在子类方法中可以具有@Nullable 或 @NotNull 注释。
* 使用带注释参数的重写/实现方法：父方法中的参数 @Nullable 注释需要针对子类方法参数的 @Nullable 注释。父方法中参数的 @NotNull 注释的方法对于子类方法参数可以具有 @Nullable 或 @NotNull 注释（或不包含它们）。

