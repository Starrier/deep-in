# HTTP/3



> * 原文地址：[Some notes about HTTP/3](https://blog.erratasec.com/2018/11/some-notes-about-http3.html?m=1)
> * 原文作者：[Errata](https://blog.erratasec.com)
> * 译文出自：[掘金翻译计划](https://github.com/xitu/gold-miner)
> * 本文永久链接：[https://github.com/xitu/gold-miner/blob/master/TODO1/some-notes-about-http3.md](https://github.com/xitu/gold-miner/blob/master/TODO1/some-notes-about-http3.md)
> * 译者：[Starrier](https://github.com/Starriers)
> * 校对者：

## 关于 HTTP/3 的一些心得

HTTP/3 即将成为以后的标准。最为协议的老用户，我觉得自己应该写点什么。

Google \(pbuh\) 拥有最受欢迎的浏览器（Chrome）和两个最受欢迎的网站（\#1 Google.com \#2 Youtube.com）。因此，谷歌手握未来 Web 协议的开发权。他们把第一次升级的协议称为 SPDY（发音是 "speedy"），也就是之后被标准化的 HTTP 的第二个版本，即 HTTP/2。他们将第二次升级的协议的称为 QUIC（发音是 "quick"），也就是即将被标准化的 HTTP/3。

目前主流的 Web 浏览器（chrome、Firefox、Edge、safari）和主流的 Web 服务器（Apache、Nginx、IIS、CloudFlare）都已经对 SPDY \(HTTP/2\) 进行了支持。许多受欢迎的网站也对其进行了支持（甚至是非谷歌站点），尽管你不想在网上看到它（用 wireshark 或者 tcpdump 进行检测），因为它一直是用 SSL 进行加密的。尽管该标准允许 HTTP/2 在 TCP 上运行，但实际上所有的应用都是 SSL 进行的。

这里有一个关于**标准**的知识点。在互联网范围以外，标准通常都是依附于法律之上，由政府进行管理。所有的主要利益相关人员在一个会议室进行制定，然后用法律强迫人们认可并使用它。然而在互联网上则有所不同，人们首先会实现标准，然后有用户的认可度决定是否开始使用。标准通常都是事实，RFCs 是为了在互联网上一句正常工作的内容所编写的，用于记录人们已经在使用的内容。SPDY 被浏览器/服务器采用的原因不仅仅是因为它被标准化，还因为互联网的主要厂商都开始添加对它的支持。QUIC 也是如此：它正在被标准化为 HTTP/3 则反映了它正在被使用，这不仅仅是一个里程碑，因为大多数人都已经开始在实践中开始应用这一协议。

QUIC 实际上更像是 TCP（TCP/2）的新版本，而不是 HTTP（HTTP/3）的新版本。因为它并没有真正改变 HTTP/2 所做的事情，而是改变了传输所做的工作方式。因此，我下面的评论重点是传输问题，而不是 HTTP 问题。

主要的重点特性是更快的连接设置和**延迟**。TCP 需要在连接建立之前来回发送多个数据包。SSL 会在加密建立之间请求来回发送多个数据包。如果有大量网络延迟，比如人们使用半秒每次的卫星互联网 ping 通信，它可能需要很长的时间来建立一个连接。通过减少往返次数，连接可以更快地建立，因此当你点击一个链接时，链接资源就会立刻响应并反馈。

下一个重点特性是**带宽**。在资源和目标之间的网络连接总是存在着带宽限制，而这总是拥塞导致的。双方都需要发现这个速度才可以让彼此以正确的速度发送数据包。发送数据包太快会导致数据包被丢弃，在没有提高速率的情况下，会给其他人造成更多的拥塞。发送数据包太慢意味着无法最大化利用网络。

为什么传统的 HTTP 在这方面会表现得如此差强人意。与网站的交互需要同时传输多种内容，而 HTTP 使用单独的 TCP 无法达到这一目的，因此浏览器会和 Web 服务器进行多个连接 \([一般为 6](https://twitter.com/tunetheweb/status/1064422216262213633)\)。可这又破坏了对带宽的估计，因此每个 TCP 连接都试图独立地执行，就好像其他连接不存在一样。SPDY 通过**多路复用**的特性解决了这一问题，它将浏览器/服务器之间的多个交互与单个带宽计算结合在一起。

QUIC 扩展了这种多路复用方式，促使在浏览器/服务器之间的多个交互处理变得更加简单，这不会导致交互之间彼此阻塞，这需要通用的带宽估算。从用户的角度来看，这将使交互更加顺畅，同时减少路由拥塞的用户体验。

现在我们讨论一下 **user-mode stacks**。这是来自于 TCP 的问题，尤其是在服务器上的表现，TCP 连接由操作系统**内核**处理，而服务本身运行在**用户模式**。跨内核/用户模式边界操作资源会导致性能问题。追踪大量 TCP 连接会导致可伸缩性问题。有些人尝试将服务器放入内核来避免过度使用的情况，这显然不是好主意。因为它会破坏操作系统的稳定性。我的解决方案是使用 BlackICE IPS 和 masscan，这是一个**用户模式驱动**的硬件。从网络芯片中直接互殴数据包到用户模式进程，绕过内核 （参阅 PoC\|\|GTFO \#15），使用自定义的 TCP 堆栈。这几年在 **DPDK** kit 中开始流行起来。

但在没有用户模式驱动的情况下，将 TCP 迁移到 UDP 也会带来相同的性能提升。与调用熟悉的 **recv\(\)** 函数一次接收单个数据包不同，你可以调用 **recvmmsg\(\)** 来同时接收一组 UDP 数据包。它仍然是一个内核/用户模式转换的情况，但在同时收到一百个包进行分摊比对每个包进行转换要好得多。

在我的测试中，使用典型的 **recv\(\)** 函数限制每秒为 5 百万个 UDP 数据包，但使用 **recvmmsg\(\)** 和其他一些优化手段（使用 RSS 的多核），在低端四核服务器上每秒可以获取 5 百万个 UDP 数据包。由于这是每个核都很优秀，移动到有 64 个核的强大服务器应该会进一步改善结果。

顺便说一句，“RSS” 是网络硬件的一个特性，它将传入的数据包分成多个接收队列。多核可伸缩性的最大问题是当两个 CPU 内核需要同时读取/修改同一件事时，共享相同的 UDP 数据包队列会变成最大的瓶颈。因此，Intel 首先和其他以太网供应商增加了 RSS，使每个核都有自己的非共享数据包队列。Linux 和其他操作系统升级了 UDP，以支持单个套接字（SO\_REUSEPORT）的多个文件描述符来处理多个队列。现在，QUIC 利用这些进步，允许每个核管理自己的 UDP 数据包流，而不存在与其他 CPU 核共享东西的可伸缩性问题。我之所以提及，是因为我曾在 2000 年时，与 Intel 硬件工程师讨论过建立多个分组队列的问题。这是一个常见的问题，也是一个显而易见的解决方案，在过去的二十年里，看到它的进展一直很有趣，知道它以 HTTP/3 的形式出现在顶层。如果没有网络硬件中的 RSS，QUIC 也不太可能会成为标准。

QUIC 另一个很酷的解决方案是**移动**支持。你的电脑或者手机会随着你移动到不同的 WIFI 网络环境而改变自身的 IP 地址。操作系统和协议没有优雅地关闭旧连接并打开新的连接。然后在 QUIC 协议下，连接的标识符不再是“套接字”（源/目的端口/地址协议组合）的传统概念，而是分配给连接的 64 位标识符。

这意味着，当你移动时，你可以在 IP 地址不断变化的情况下，持续地从 YouTube 获取一个数据流，或者继续进行视频电话呼叫，而不会被中断。几十年来，互联网工程师一直在与“移动 IP”作斗争，试图相处一个可行的解决方案。他们关注的是端到端的原则，即在你移动的时候，以某种方式保持一个固定的 IP 地址，但这不是一个实际的解决方案。很荣幸可以看到在现实世界中有 QUIC/HTTP/3 这样一个可行的方案，最终解决了这个问题。

如何使用这些新的传输工具？几十年来，网络编程的标准一直是**传输层 API**，即“套接字”。也就是调用像 **recv\(\)** 这样的函数来接收代码中的数据包。在 QUIC/HTTP/3，我们不再有操作系统传输层 API。取而代之的是一个更高层次的特性，你刻意在类似 **GO** 编程语言中使用它，或者在 OpenResty Nginx Web 服务器中使用 Lua。

我之所以会提及，是因为你在 OSI 模型的教育学习中错过了一件事，那就是它最初设想的是每个人都会编写到应用层（7）API，而不是传输层（4）API。应该有像**应用程序服务元素**之类的能以标准方式为不同的应用程序处理租入文件传输和消息传递之类的操作。尤其是包括 **Go、QUIC、protobufs** 等在 Google 的驱动下，人们会越来越倾向于这种模式。

我之所以会提到这一点，是因为 Google 和 微软之间形成了鲜明的对比。微软拥有一个流行的操作系统，所以它的创新是有它在该操作系统内所能做的事情驱动的。Google 的创新是由它能在操作系统上安装的东西所驱动的。还有 Facebook 和 Amazon 本身，它们必须在谷歌所提供给他们的堆栈之上（或者外部）进行创新。世界上排名前五位的公司依次是 Apple、Google、Microsoft、Amazon、Facebook，因此每个公司推动创新的位置都很重要。

**结论**

当 TCP 在 20 世纪 70 年代被创建时，它是伟大的。它所处理的操作，诸如拥塞，远远好过相互竞争的协议。尽管人们声称 IPv4 没有预料到超过 40 亿个地址的事情，但它比 70 年代和 80 年代的竞争设计要好得多。从 IPv4 升级到 IPv6 很大程度上维护了 IP 的地位。从 TCP 升级到 QUIC 类似地是基于 TCP 的伟大之处，但将其扩展到现代需求。令人惊讶的是，TCP 的应用竟然持续了这么长时间，而且还没有进行升级。

> 如果发现译文存在错误或其他需要改进的地方，欢迎到 [掘金翻译计划](https://github.com/xitu/gold-miner) 对译文进行修改并 PR，也可获得相应奖励积分。文章开头的 **本文永久链接** 即为本文在 GitHub 上的 MarkDown 链接。

> [掘金翻译计划](https://github.com/xitu/gold-miner) 是一个翻译优质互联网技术文章的社区，文章来源为 [掘金](https://juejin.im) 上的英文分享文章。内容覆盖 [Android](https://github.com/xitu/gold-miner#android)、[iOS](https://github.com/xitu/gold-miner#ios)、[前端](https://github.com/xitu/gold-miner#前端)、[后端](https://github.com/xitu/gold-miner#后端)、[区块链](https://github.com/xitu/gold-miner#区块链)、[产品](https://github.com/xitu/gold-miner#产品)、[设计](https://github.com/xitu/gold-miner#设计)、[人工智能](https://github.com/xitu/gold-miner#人工智能)等领域，想要查看更多优质译文请持续关注 [掘金翻译计划](https://github.com/xitu/gold-miner)、[官方微博](http://weibo.com/juejinfanyi)、[知乎专栏](https://zhuanlan.zhihu.com/juejinfanyi)。

