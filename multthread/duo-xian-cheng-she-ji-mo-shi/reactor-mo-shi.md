# Reactor 模式

**多线程并发模式，一个连接一个线程**

**优点**

 一定程度上极大地提高了服务器的吞吐量，因为之前的请求在read阻塞以后，不会影响到后续的请求，因为他们在不同的线程中。这也是为什么通常会讲“一个线程只能对应一个socket”的原因。另外有个问题，如果一个线程中对应多个socket连接不行吗？语法上确实可以，但是实际上没有用，每一个socket都是阻塞的，所以在一个线程里只能处理一个socket，就算accept了多个也没用，前一个socket被阻塞了，后面的是无法被执行到的。

**缺点**

缺点在于资源要求太高，系统中创建线程是需要比较高的系统资源的，如果连接数太高，系统无法承受，而且，线程的反复创建-销毁也需要代价。

改进方法是：

采用基于事件驱动的设计，当有事件触发时，才会调用处理器进行数据处理。使用Reactor模式，对线程的数量进行控制，一个线程处理大量的事件。

实际上的Reactor模式，是基于Java NIO的，在他的基础上，抽象出来两个组件——Reactor和Handler两个组件：

1. Reactor：负责响应IO事件，当检测到一个新的事件，将其发送给相应的Handler去处理；新的事件包含连接建立就绪、读就绪、写就绪等。
2. Handler:将自身（handler）与事件绑定，负责事件的处理，完成channel的读入，完成处理业务逻辑后，负责将结果写出channel。

### 单线程 Reactor 模式

![](../../../.gitbook/assets/reactorsingle.png)

**单线程模式的缺点**

1.  当其中某个 handler 阻塞时， 会导致其他所有的 client 的 handler 都得不到执行， 并且更严重的是， handler 的阻塞也会导致整个服务不能接收新的 client 请求\(因为 acceptor 也被阻塞了\)。 因为有这么多的缺陷， 因此单线程Reactor 模型用的比较少。这种单线程模型不能充分利用多核资源，所以实际使用的不多。
2. 因此，单线程模型仅仅适用于 handler 中业务处理组件能快速完成的场景

### 多线程 Reactor 模式

在线程Reactor模式基础上，做如下改进：

1. 将Handler处理器的执行放入线程池，多线程进行业务处理。
2. 而对于Reactor而言，可以仍为单个线程。如果服务器为多核的CPU，为充分利用系统资源，可以将Reactor拆分为两个线程。

![](../../../.gitbook/assets/reactormultipart.png)

### Reactor 特点

#### 优点

1. 响应快，不必为单个同步时间所阻塞，虽然Reactor本身依然是同步的
2. 编程相对简单，可以最大程度的避免复杂的多线程及同步问题，并且避免了多线程/进程的切换开销
3. 可扩展性，可以方便的通过增加Reactor实例个数来充分利用CPU资源
4. 可复用性，reactor框架本身与具体事件处理逻辑无关，具有很高的复用性；

#### 缺点

1. 相比传统的简单模型，Reactor增加了一定的复杂性，因而有一定的门槛，并且不易于调试。
2. Reactor模式需要底层的Synchronous Event Demultiplexer支持，比如Java中的Selector支持，操作系统的select系统调用支持，如果要自己实现Synchronous Event Demultiplexer可能不会有那么高效
3. Reactor模式在IO读写数据时还是在同一个线程中实现的，即使使用多个Reactor机制的情况下，那些共享一个Reactor的Channel如果出现一个长时间的数据读写，会影响这个Reactor中其他Channel的相应时间，比如在大文件传输时，IO操作就会影响其他Client的相应时间，因而对这种操作，使用传统的Thread-Per-Connection或许是一个更好的选择，或则此时使用改进版的Reactor模式如Proactor模式。

参考地址：[http://www.cnblogs.com/crazymakercircle/p/9833847.html](http://www.cnblogs.com/crazymakercircle/p/9833847.html)

