---
title: while-if
date: 2019-02-19 09:51:33
tags: Thread
index_img: ../post_img/while-if.jpeg
---

## While-if

举个例子，一个生产者消费者模型的任务队列，一个生产者一次可能放入多个任务，然后用notifyAll通知消费者，但是并非所有被唤醒的消费者都能取到一个任务，那么队列被读空了之后的消费者肯定得继续await。如果你用if来判断，这个消费者第二次被notify的时候就不会再次判断!(ok to proceed)这个条件了，如果这个时候这个消费者又一次没抢到任务，但是代码还是往下执行了，轻则空指针异常，重了干出什么事情来都说不定了。

所以必须用while来检查!(ok to proceed)，这样可以保证每次被唤醒都会检查一次条件。