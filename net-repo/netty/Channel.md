# Channel 

基本的 IO 操作（bind，connect，read，write）依赖于底层网络传输所提供的原语。Channel 的部分 API

1. EmbeddedChannel
2. LocalServerChannel
3. NioDatagramChannel
4. NioSctpChannel
5. NioSocketChannel

## EventLoop

1. 一个 EventLoopGroup 包含一个或多个 EventLoop
2. 一个 EventLoop 在它的生命周期内只和一个 Thread 绑定
3. 所有由 EventLoop 处理的 IO 事件都将在它专有的 Thread 上被处理
4. 一个 Channel 在它的生命周期内只注册一个 EventLoop
5. 一个 EventLoop 可能会被分配给一个或多个 Channel

## ChannelFuture 接口

## ChannelHandler

## ChannelPipeline

在 Netty 中，有两种发送消息的方式。

1. 你可以直接写到 Channel 中（消息从 ChannelPipeline 的尾端开始流动）
2. 也可以写到和 ChannelHandler 相关连的 ChannelHandlerContext 对象中。（消息从 ChannelPipeline 中的下一个 ChannelHandler 开始流动）