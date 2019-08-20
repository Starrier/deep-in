---
title: h5-storage
date: 2019-03-12 22:46:24
tags: Front-end
index_img: ../post_img/netty-reactor.jpeg
---

# localStorage、sessionStorage、cookie、session

H5 中的 web Storage 定义了两种存储方式：sessiongStorage 和 localStorage。

web Storage 提供了 storage 事件，当键值改变或 clear 的时候，就会触发 storage 事件：

```js
if(window.addEventListener){
    window.addEventListener("storage",handle_storage,false);
}else if(window.attachEvent){
    window.attchEvent("onstorage",handle_storage);
}

function handle_storage(e){
    if(!e){
        e=windwo.event;
    }
}
```

#### 操作方式

``` js
window.localStorage
window.sessionStorage
```

它们只存储字符串类型，对于复杂的对象可以使用 ECMAScript 提供的 JSON 对象的 stringify 和 parse 来处理。

Web Storage 的优点：

1. 存储空间更大：
2. 节省网络带宽：
3. 快速显示：
4. 安全性：
5. 对于只需要在用户浏览一组页面期间保存而关闭浏览器后就可以丢弃的数据，sessionStorage 非常适合。

WebStorage 提供的一些操作

| 方法|作用|
|---|---|
| setItem(key,value)| 保存数据，以键值对的方式存储|
| getItem(key)| 获取数据|
| removeItem(key)| 删除单个数据|
| clear()| 删除所有数据|
| key(index)| 获取某个索引的 key|

storage 事件对象的具体属性如下表：

|Property| Type| Description|
|---|---|---|
|key |String |The named key that was added, removed, or moddified|
|oldValue |Any    |The previous value(now overwritten), or null if a new item was added|
|newValue |Any| The new value, or null if an item was added|
|url/uri |String| The page that called the method that triggered this change|

## localStorage

localStorage

localStorage 用于持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的。localStorage 在所有同源窗口都是共享的。

 1. localStorage 扩展了 cookie 的 4 k 限制
 2. localStorage 可以将第一次请求的数据直接存储到本地，这个相当于一个 5 M 大小的针对于前端页面的数据库，相比于 cookie，可以节省带宽，但这个只有在高版本的浏览器中才支持
 3. localStorage 遵循同源策略，不同的网站之间不会共享相同的 localStorage
 4. 只支持 String 类型的存储

## sessionStorage

 sessionStorage 用于存储一个会话（session）中的数据，这些数据仅在当前浏览器窗口关闭前有效，不能再其他浏览器窗口中共享。

## cookie

cookie 用于辨识用户身份，进行会话跟踪而存储在本地终端的数据，最典型的案例就是通过访问 cookie 判断注册用户是否已经登录过网站，可以利用 cookies 跟踪统计用户访问该网站的习惯，比如什么时间访问的，访问了哪些页面，在每个页面的停留时间等。数据通常经过加密，一个 cookie 由名称，值，域，路径，失效时间，安全标准六部分组成。cookie 是以文本的方式保存在客户端上，每次请求时都带上它。

操作方法：

1. 前端可以使用 document.cookie 来获取当前页面的 cookie，进行操作。
2. 在后端可以设置 set-cookie 响应头来设置 cookie。

**缺点**

1. 大小受限
2. 安全性低
3. 用户可以操作 cookie（禁用），导致功能受限
4. 有些状态难以保存在客户端
5. 每次访问都要传送 cookie 给服务器，浪费带宽
6. cookie 数据有路径（path）概念，可以限制 cookie 值属于某个路径下。

## session

当服务器收到请求需要创建 session 对象时，首先会检查客户端请求中是否包含 sessionId，如果有，服务器将根据该 id 返回对应 session 对象。如果客户端请求中没有 sessionId，服务器会创建 session 对象，并把 sessionId 在本次响应中返回给客户端。通常使用 cookie 方式存储 sessionId 到客户端，在交互中浏览器按照规则将 sessionId 发送给服务器。如果用户禁用 cookie，则要使用 url 重写，可以通过 response.encodeURL(url) 进行实现；API 对 encodeURL 的结束为，当浏览器支持 cookie 时，url 不做任何处理；当浏览器不支持 cookie 的时候，将会重写 URL 将 sessionId 拼接到访问地址后。

session 用于保存每个用户的专用信息，变量的值保存在服务器端，通过 sessionId 来区分不同的客户。

**缺点**：

 1. session 保存的东西越多，就越占用浏览器内存，对于用户在线人数较多的网站，服务器的内存压力会较大。
 2. 依赖于 cookie（sessionId 保存在 cookie），如果禁用 cookie，则要使用 url 重写，不安全。
 3. 创建 session 变量有很大的随意性，可随时调用，不需要开发者做精确地处理，所以，过度使用 session 变量将会导致代码不可读且难以维护。

### sessionStorage、localStorage 和 cookie 的区别

 1. **工作方式**：cookie 数据始终在同源的 http 请求中携带（即使不需要），即 cookie 在浏览器和服务器之间来回传递；sessionSotrage 和 localStorage 不会主动把数据发给服务器，仅在本地保存。
 2. **存储大小限制**：cookie 数据不能超过 4 k，同时因为每次 htpp 请求都会携带 cookie，所以 cookie 适合保存很小的数据，如会话标识，sessionStorage 和 localStorage 虽然也有存储大小的限制，但比 cookie 大得多，可以达到 5 M 甚至更大。
 3. **数据有效期**：sessionSotrage 仅在当前浏览器窗口关闭前有效；localStorage 始终有效，窗口或浏览器关闭也一直保存；cookie 的有效时间由自己设置，即使窗口或浏览器关闭。
 4. **作用域不同**：sessionStorage 不在不同的浏览器窗口中共享，即使是同一个页面；localStorage 字啊所有同源窗口中都是共享的；cookie 也是在所有同源窗口中共享的。cookie 数据还有路径（path）的概念，可以限制 cookie 只属于某个路径下。
 5. **API 不同**：Web Storage 支持事件通知机制，可以将数据更新的通知发送给监听者。web Storage 的 API 接口使用更方便。
 6. **存储位置**：cookie 保存在浏览器端，session 保存在服务端。