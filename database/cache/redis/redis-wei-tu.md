# Redis 位图

{% hint style="warning" %}
 **Redis 的位数组是自动扩展，如果设置了某个偏移位置超出了现有的内容范围，就会自动将位数组进行零扩充。**
{% endhint %}

### Bitmap\(Bitset\)

 Bitmap 是一串连续的二进制数字（0 或 1），每一位所在的位置为便宜（offset），在 bitmap 上可执行 AND、OR、XOR 以及其他位操作。

 Redis允许使用二进制数据的Key\(binary keys\) 和二进制数据的Value\(binary values\)。Bitmap就是二进制数据的value。Redis的 setbit\(key, offset, value\)操作对指定的key的value的指定偏移\(offset\)的位置1或0，时间复杂度是O\(1\)。

###  redis.setbit\(daily\_active\_users, user\_id, 1\)



如果对应位的字节是不可打印字符，redis-cli 会显示该字符的 16 进制形式。

```text
127.0.0.1:6379> setbit x 0 1
(integer) 0
127.0.0.1:6379> setbit x 1 1
(integer) 0
127.0.0.1:6379> get x
"\xc0"
```

### 统计和查找

 Redis 提供了位图统计指令 bitcount 和位图查找指令 bitpos，bitcount 用来统计指定位置范围内 1 的个数，bitpos 用来查找指定范围内出现的第一个 0 或 1。

### 魔术指令 bitfield

前文我们设置 \(setbit\) 和获取 \(getbit\) 指定位的值都是单个位的，如果要一次操作多个位，就必须使用管道来处理。

不过 Redis 的 3.2 版本以后新增了一个功能强大的指令，有了这条指令，不用管道也可以一次进行多个位的操作。

bitfield 有三个子指令，分别是 get/set/incrby，它们都可以对指定位片段进行读写，但是最多只能处理 64 个连续的位，如果超过 64 位，就得使用多个子指令，bitfield 可以一次执行多个子指令。

