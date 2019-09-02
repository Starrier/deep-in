# Extension

单进程多线程模型：

MySQL Memcached Oracle（windows版本）

多进程模型

Oracle（linux 版本）

Ngnix 有两类进程，一类是 Master 进程，另一类是 Worker 进程

1. 单进程启动：此时系统中仅有一个进程，该进程既充当 Master 进程角色，又充当 Worker 进程角色。
2. 多进程启动：此时系统中有且仅有一个 Master 进程，至少有一个 Worker 进程工作。
3. Master 进程主要进行一些全局性的初始化工作以及管理 Worker 工作，事件处理都是在 Worker 中进行的。



