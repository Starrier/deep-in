# Redis - sorted-set



1. 类似map是一个key-value对，但是有序的。value是一个浮点数，称为score，内部是按照score从小到大排序
2. 内部结构以ziplist或skiplist+hashtable来实现

