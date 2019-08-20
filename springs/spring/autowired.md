# @Autowired

静态变量或静态语句块–&gt;实例变量或初始化语句块–&gt;构造方法–&gt;@Autowired

好的，有了上面的铺垫，我们来看看下面的代码。

```text
@Autowired
private User user;
private School school;

public UserServiceImpl(){
    this.school.id = user.getSchoolId();
}
```

由于java先执行构造方法，导致

this.school = user.getSchool\(\);

报空指针异常（虽然这个例子正常人都不会这样写...

解决办法就是使用构造器注入了

```text
private User user;
private String schoolId;

@Autowired
public UserServiceImpl(User user){
    this.user = user;
    this.schoolId = user.getSchoolId();
}
```

## 四

而且若是你是个单例的模式（bean没写@scope，默认为单例，

那么spring还建议你在bean的声明上加final，这个的解析就简单粗暴了。

因为加上final只会在程序启动的时候初始化一次，并且在程序运行的时候不会再改变。

官方的建议例子

```text
    private final EnterpriseDbService service;

    @Autowired
    public EnterpriseDbController(EnterpriseDbService service) {
       this.service = service;
    }
```

