# Sentinel

提供了两种流量统计方式：

1.统计并发线程数，当线程数超出某个限定阀值，新的请求就会被立即拒绝。
2.统计 QPS。当 QPS 超出某个设定的阀值，系统可以通过拒绝，冷启动，匀速器三种方式应对。

https://www.cnblogs.com/SaraMoring/p/5713732.html

