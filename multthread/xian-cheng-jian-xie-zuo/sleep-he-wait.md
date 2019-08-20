# sleep\(\) 和 wait\(\)

sleep\(\)  属于 Thread 类，wait\(\) 属于 Object 类

sleep\(\) 方法导致了程序暂停执行指定的时间，让出 CPU 给其他线程，但是他的监控状态依然保持着，当指定的时间到了又会自动恢复运行状态。在调用sleep\(\)  方法的过程中，线程不会释放对象锁。

调用 wait\(\) 方法时，线程会放弃对象锁，进入等待此对象的等待锁池，只有调用针对此对象的 notify\(\) 方法后，本线程才进入对象锁定池准备。

 Thread.Sleep\(0\)的作用是“触发操作系统立刻重新进行一次CPU竞争”。

1.  wait、notify、notifyAll 只能在同步控制方法或者同步控制块（synchronize）里面使用，而 sleep 可以在任何地方使用
2. sleep必须捕获异常，而 wait、notify、notifyAll 不需要捕获异常。
3. sleep 是静态类方法，谁调用谁 sleep，A 调用了 B 的sleep，A sleep，B 调用 sleep B睡觉。
4. sleep 可以用时间指定自身醒来的时间，如果时间不到，只能调用 Interrupt\(\) 强行打断。

### Yield\(\) 

停止当前线程，让同等优先权的线程运行。如果没有同等优先权的线程，那么Yieid 方法将不会起作用。

线程结束的标志：run\(\) 方法结束

锁被释放的标志：synchronize 块或方法结束。

如果线程 A 希望理解结束线程 B，则可以对线程 B 对应的 Thread 实例调用 Interrupt\(\)。

{% hint style="warning" %}
如果此刻线程 B 正在 wait/sleep/join，则线程 B 会立刻抛出 InterruptedException，在 catch{} 中直接 return 即可安全地结束线程。
{% endhint %}

  

