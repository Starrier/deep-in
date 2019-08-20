# 普通哈希算法 和一致性 hash 算法

1. 普通哈希：也称硬哈希，采用简单取模的方式，将机器进行散列，这在cache环境不变的情况下能取得让人满意的结果，但是当cache环境动态变化时，这种静态取模的方式显然就不满足单调性的要求（当增加或减少一台机子时，几乎所有的存储内容都要被重新散列到别的缓冲区中）。
2. 一致性哈希：将机器节点和key值都按照一样的hash算法映射到一个0~2^32的圆环上。当有一个写入缓存的请求到来时，计算Key值k对应的哈希值Hash\(k\)，如果该值正好对应之前某个机器节点的Hash值，则直接写入该机器节点，如果没有对应的机器节点，则顺时针查找下一个节点，进行写入，如果超过2^32还没找到对应节点，则从0开始查找（因为是环状结构）。为了更可能的满足平衡性，可以引入虚拟节点，即一个实体节点映射到多个虚拟节点。
