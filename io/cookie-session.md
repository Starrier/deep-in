---
title: cookie-session
date: 2019-03-05 22:30:54
tags: front
index_img: ../post_img/cookie-ssion.jpeg
---

## Cookie and Session

会话用于跟踪用户的整个会话。包括 Cookie 和 Session。Cookie 通过在客户端记录信息来确定用户身份，Session 通过在服务器端记录信息来确定用户身份。

### Cookie

Cookie 技术是客户端的解决方案，Cookie 是由服务器发给客户端的特殊信息，它们以文本的方式存储在客户端。之后，客户端

#### 路径

domain 属性决定运行访问 Cookie 域名，path 属性决定允许访问 Cookie 路径（ContextPath）。**当前页面只能获取属于它自己 path 的 Cookie。

 1. domain 表示的是 cookie 所在的域，默认为请求的地址