# DNS 原理

## 为什么需要DNS解析域名为IP地址？

网络通讯大部分是基于 TCP/IP 的，而 TCP/IP 是基于 IP 地址的，所以计算机在网络上进行通讯时只能识别如 “202.96.134.133” 之类的 IP 地址，而不能认识域名。我们无法记住 10 个以上 IP 地址的网站，所以我们访问网站时，更多的是在浏览器地址栏中输入域名，就能看到所需要的页面，这是因为有一个叫 “DNS服务器” 的计算机自动把我们的域名“翻译”成了相应的 IP 地址，然后调出 IP 地址所对应的网页。

## DNS 服务的工作过程

当 DNS 客户机需要查询程序中使用的名称时，它会查询本地DNS 服务器来解析该名称。客户机发送的每条查询消息都包括3条信息，以指定服务器应回答的问题：

 - 指定的 DNS 域名，表示为完全合格的域名 (FQDN) 。
 - 指定的查询类型，它可根据类型指定资源记录，或作为查询操作的专门类型。
 - DNS域名的指定类别。

对于DNS 服务器，它始终应指定为 Internet 类别。例如，指定的名称可以是计算机的完全合格的域名，如 im.qq.com，并且指定的查询类型用于通过该名称搜索地址资源记录。

DNS 查询以各种不同的方式进行解析。客户机有时也可通过使用从以前查询获得的缓存信息就地应答查询。DNS 服务器可使用其自身的资源记录信息缓存来应答查询，也可代表请求客户机来查询或联系其他 DNS 服务器，以完全解析该名称，并随后将应答返回至客户机。这个过程称为递归。另外，客户机自己也可尝试联系其他的 DNS 服务器来解析名称。如果客户机这么做，它会使用基于服务器应答的独立和附加的查询，该过程称作迭代，即DNS服务器之间的交互查询就是迭代查询。

1. 在浏览器中输入 `www.qq.com`,操作系统会先检查自己本地的 hosts 文件是否有这个网址映射关系，如果有，就先调用这个 IP 地址映射，完成域名解析。
2. 如果 hosts 里没有这个域名的映射，则查找本地 DNS 解析器缓存，是否有这个网址映射关系，如果有，直接返回，完成域名解析。
3. 如果 hosts 与本地 DNS 解析器缓存中都没有相应的网址映射关系，首先会找 TCP/IP  参数中设置的首选 DNS 服务器，再次我们叫它本地 DNS 服务器，此服务器收到查询时，如果查询的域名，包含在本地配置区域资源中，则返回解析结果给客户机，完成域名解析，此解析具有权威性。
4. 如果要查询的域名，不由本地 DNS 服务器区域解析，但该服务器已缓存了此网址映射关系，则调用这个 IP 地址映射，完成域名解析。
5. 如果本地 DNS 服务器本地区域文件与缓存解析都失效，则根据本地 DNS 服务器的设置（是否设置转发器）进行查询，如果未用转发模式，本地 DNS 就把

解析顺序

1. 浏览器缓存：

> 当用户通过浏览器访问某个域名时，浏览器首先会在自己的缓存中查找是否有该域名对应的 IP 地址（曾经访问过该域名且没有清空缓存）

2. 系统缓存

> 当浏览器缓存中无域名对应 IP 则会自动检查用户计算机系统 HOSTS 文件 DNS 缓存是否有该域名对应 IP

3. 路由器缓存

> 当浏览器及系统缓存中均无域名对应 IP 则进入路由器缓存中检查，以上三步均为客户端的 DNS 缓存。

4. ISP（互联网服务提供商）DNS 缓存

> 当在用户客户端找不到域名对应 IP 地址，