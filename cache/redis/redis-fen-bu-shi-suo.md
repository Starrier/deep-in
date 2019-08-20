# Redis - 分布式锁

### 主要使用的命令

1. setnx key val。当且仅当key不存在时，set一个key为val的字符串，返回1；若key存在，则什么都不做，返回0。
2. expire key timeout。为key设置一个超时时间，单位为second，超过这个时间锁会自动释放，避免死锁。
3. delete key。删除锁

### 思路

1. 使用setnx加锁，如果返回1，则说明加锁成功，并设置超时时间，避免系统挂了，锁没法释放。在finally中delete删除锁释放。
2. 如果需要设置超时等待时间，则可以加个while循环，在获取不到锁的情况下，进行循环获取锁，超时了则退出。

