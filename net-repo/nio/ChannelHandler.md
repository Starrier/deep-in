# ChannelHandler

入站消息会被解码（即，通常为从字节转换为 Java 对象），出站会被编码（从当前格式转换为字节）。

## 编码器

## 解码器

## SimpleChannelInboundHandler

引导

一种用于客户端（BootStrap）另一种用于服务器（ServerBootStrap）。无论使用那种协议或者处理哪种类型的数据，唯一决定它使用哪种引导类的是它作为一个客户端还是服务器。

| 类别 | BootStrap | ServerBootStrap |
| -- | -- | -- |
| 网络编程中的作用 | 连接到远程主机和端口 |
| EventLoopGroup 的数目 | 1 | 2 |

服务器需要两组不用的 Channel。第一组将只包含一个 ServerChannel，代表服务器自身的已绑定到某个本地端口的正在监听的套接字。而第二组将包含所有已创建的用来处理传入客户端链接（对于每个服务器已经接受的连接都有一个）的 channel。