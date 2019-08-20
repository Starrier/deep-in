# @ResponseStaus

##  @ResponseStatus

1. value : HttpStauts 
2. reason : error message

### 1. create custom exception class

```java
@ResponseStatus(value=HttpStatus.FORBIDDEN,reason="User Not Match")
public class UserNotMatchException extends RunntimeException{}
```

### 2. create target method to throw exception

```java
@RequestMapping("/user")
public String login(User user){ 
       //TODO
        if(user.isNotExist){
         throw new UserNotMatchException();
        } 
        //TODO
    }
```

{% hint style="danger" %}
@ResponseStatus 修饰目标方法，无论它执行方法过程中有没有异常产生，用户都会得到异常的界面，而目标方法正常执行
{% endhint %}

```java
@ExceptionHandler( value=UserNotMatchException.class )
public void  User() {
    //TODO
    if(user.isNotExist){
    throw new UserNotMatchException();
    }
    //TODO
}
```

#### resolution:

```text
@ExceptionHandler( value=UserNotMatchException.class )
public ResponseEntity<String> login(HttpServletResponse response) {
    response.sendRedirect(location/of/your/page);
    return new ResponseEntity<String>(HttpStatus.NOT_FOUND);
}
```

#### Tips：

1. while explain the reason with chinese,unforeseen circumstances may lead to confusion, messy code,the resolution is add the configuration in the applocation.properties/application.yml with spring.http.encoding.force=true

{% file src="../../.gitbook/assets/responsestatus.md" %}

