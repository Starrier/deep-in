---
description: 重试回退策略(立即重试还是等待一会儿）
---

# @Backoff

不设置参数时，默认使用 FixedBackOffPolicy，重试等待 1000ms

只设置 delay\(\) 属性时，使用 FixedBackOffPolicy，重试等待指定的毫秒数

设置 delay 和 maxDelay

