# 源头之一：缓存导致的可见性问题

![avatar](../Pics/ch1-1.png)

- 一个线程对共享变量的修改，另外一个线程能够立刻看到，我们称为**可见性**。
- 多核时代，每颗 CPU 都有自己的缓存，这时 CPU 缓存与内存的数据一致性就没那么容易解决了，当多个线程在不同的 CPU 上执行时，这些线程操作的是不同的 CPU 缓存。比如下图中，**线程 A 操作的是 CPU-1 上的缓存，而线程 B 操作的是 CPU-2 上的缓存**，很明显，这个时候线程 A 对变量 V 的操作对于线程 B 而言就不具备可见性了。

# 源头之二：线程切换带来的原子性问题

![avatar](../Pics/ch1-2.png)

- **我们把一个或者多个操作在 CPU 执行的过程中不被中断的特性称为原子性。**

# 源头之三：编译优化带来的有序性问题
```
public class Singleton {
  static Singleton instance;
  static Singleton getInstance(){
    if (instance == null) {
      synchronized(Singleton.class) {
        if (instance == null)
          instance = new Singleton();
        }
    }
    return instance;
  }
}
```
-  getInstance() 方法的问题:
- 我们以为的new操作应该是：
  - 分配一块内存 M；
  - 在内存 M 上初始化 Singleton 对象；
  - 然后 M 的地址赋值给 instance 变量。
- 实际上优化后的执行路径却是这样的：
  - 分配一块内存 M；
  - 将 M 的地址赋值给 instance 变量；
  - 最后在内存 M 上初始化 Singleton 对象。

# 课后思考：在 32 位的机器上对 long 型变量进行加减操作存在并发隐患

long类型64位，所以在32位的机器上，对long类型的数据操作通常需要多条指令组合出来，无法保证原子性