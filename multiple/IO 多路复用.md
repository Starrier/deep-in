# IO 多路复用

这种 IO 方式也称为**事件驱动IO**。select/epoll 的好处在于单个 process 可以同时处理多个网络连接的 IO。基本原理是，select/epoll 这个函数会不断的轮询所负责的所有 socket，当某个 socket 有数据到达时，就通知用户进程，它的流程图是：

当用户进程调用了 select 时，那么整个进程都会被 block，而同时，kernel 会“监视”所有 select 负责的 socket，当任何一个 socket 中的数据准备好了，select 就会返回。这个时候用户进程再调用 read 操作，将数据从 kernel 拷贝到用户进程。

这个模型相对于 阻塞 IO，事实上会更差一些，因为它调用了两个系统调用（select，recvfrom），而阻塞 IO 只调用了（revcfrom）。但是 用 select 的优势在于它可以同时处理多个connection。

注意

1. 如果处理的链接数不是很高的话，使用 select/epoll 不一定比使用 多线程+阻塞IO 模型的性能好，可能会更差。select/epoll 的优势并不是对于单个链接能处理的很快，而是在于可以处理更多的链接。

2. 多路复用模型中，对于每一个 socket，一般都设置成为 非阻塞，但其实整个 process 一直被 阻塞，只是 process 是被 select 这个函数阻塞，而不是 socket IO 被阻塞。

因此，select 的优势在于可以处理多个连接，不适用单链接。

## 异步 IO （AIO）

用户进程发起 read 操作之后，立刻开始做其他事情。对于内核来说，当它收到一个异步 read 后，首先会立即返回，所以不会对用户进程产生任何阻塞。然后，等内核等待数据准备完成，将数据拷贝到用户内存，当这一切完成后，内核会给用户发送一个信号，告诉他 read 操作完成了。

### select

```c++
int select(int nfds, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);
```

select的第一个参数nfds为fdset集合中最大描述符值加1，fdset是一个位数组，其大小限制为__FD_SETSIZE（1024），位数组的每一位代表其对应的描述符是否需要被检查。第二三四参数表示需要关注读、写、错误事件的文件描述符位数组，这些参数既是输入参数也是输出参数，可能会被内核修改用于标示哪些描述符上发生了关注的事件，所以每次调用select前都需要重新初始化fdset。timeout参数为超时时间，该结构会被内核修改，其值为超时剩余的时间。

select 调用步骤

1. （1）使用copy_from_user从用户空间拷贝fdset到内核空间

（2）注册回调函数__pollwait

（3）遍历所有fd，调用其对应的poll方法（对于socket，这个poll方法是sock_poll，sock_poll根据情况会调用到tcp_poll,udp_poll或者datagram_poll）

（4）以tcp_poll为例，其核心实现就是__pollwait，也就是上面注册的回调函数。

（5）__pollwait的主要工作就是把current（当前进程）挂到设备的等待队列中，不同的设备有不同的等待队列，对于tcp_poll 来说，其等待队列是sk->sk_sleep（注意把进程挂到等待队列中并不代表进程已经睡眠了）。在设备收到一条消息（网络设备）或填写完文件数 据（磁盘设备）后，会唤醒设备等待队列上睡眠的进程，这时current便被唤醒了。

（6）poll方法返回时会返回一个描述读写操作是否就绪的mask掩码，根据这个mask掩码给fd_set赋值。

（7）如果遍历完所有的fd，还没有返回一个可读写的mask掩码，则会调用schedule_timeout是调用select的进程（也就是 current）进入睡眠。当设备驱动发生自身资源可读写后，会唤醒其等待队列上睡眠的进程。如果超过一定的超时时间（schedule_timeout 指定），还是没人唤醒，则调用select的进程会重新被唤醒获得CPU，进而重新遍历fd，判断有没有就绪的fd。

（8）把fd_set从内核空间拷贝到用户空间。

缺点

