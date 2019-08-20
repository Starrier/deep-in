# Feign 返回图片流处理方式

通过 Feign 返回图片一般为字节数组：

```java
@RequestMapping(value ="createImageCode")
public byte[] createImageCode(@ResquestParam("imageKey") String  imageKey{}
```

使用 Feign 时，可以将流转换为字节数组传递，但因为 Controller 层的返回不能直接返回 byte，因此需要将 Feign 的返回值修改为 response：

```java
@RequestMapping(value ="createImageCode")
public Response createImageCode(@ResquestParam("imageKey") String  imageKey{}
```

