本文将对 synchronized 关键字和 ReentrantLock 做一个详细的比较。



# 一、synchronized

synchronized 可以用来修饰以下 3 个层面：

1. 修饰实例方法；

2. 修饰静态类方法；

3. 修饰代码块。


## 1、synchronized 修饰实例方法

![image-20210104171100957](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104171100957.png)

这种情况下的**锁对象是当前实例对象**，因此只有同一个实例对象调用此方法才会产生互斥效果，不同实例对象之间不会有互斥效果。比如如下代码：

![image-20210104171846338](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104171846338.png)

上述代码，在不同的线程中调用的是不同对象的 printLog 方法，因此彼此之间不会有排斥。运行效果如下：

![image-20210104172102512](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104172102512.png)

可以看出，两个线程是交互执行的。

如果将代码进行如下修改，两个线程调用同一个对象的 printLog 方法：

![image-20210104172224988](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104172224988.png)

则执行效果如下：

![image-20210104172301131](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104172301131.png)

可以看出：只有某一个线程中的代码执行完之后，才会调用另一个线程中的代码。也就是说此时两个线程间是互斥的。

#### 2、修饰静态类方法

**如果 synchronized 修饰的是静态方法，则锁对象是当前类的 Class 对象**。因此即使在不同线程中调用不同实例对象，也会有互斥效果。

将 SynchronizedMehtods 中的 printLog 修改为静态方法，如下：

![image-20210104172458909](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104172458909.png)

执行后的打印效果如下：

![image-20210104172626694](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104172626694.png)

可以看出，两个线程还是依次执行的。

#### 3、synchronized 修饰代码块

除了直接修饰方法之外，synchronized 还可以作用于代码块，如下代码所示：

![image-20210104173719274](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104173719274.png)

synchronized 作用于代码块时，**锁对象就是跟在后面括号中的对象**。上图中可以看出任何 Object 对象都可以当作锁对象。

执行结果如下：

![image-20210104173810420](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104173810420.png)

### 3.1 示例2

![image-20210312101830652](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210312101830652.png)

结果：

![image-20210312101950258](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210312101950258.png)

# 二、Synchronized实现细节

synchronized 既可以作用于方法，也可以作用于某一代码块。但在实现上是有区别的。 比如如下代码，使用 synchronized 作用于代码块：

![image-20210104174454192](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104174454192.png)

使用 javap 查看上述 test1 方法的字节码，可以看出，编译而成的字节码中会包含 monitorenter 和 monitorexit 这两个字节码指令。如下所示：

![image-20210104174841765](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104174841765.png)

可以发现，**上面字节码中有 1 个 monitorenter 和 2 个 monitorexit。这是因为虚拟机需要保证当异常发生时也能释放锁**。因此 2 个 monitorexit 一个是代码正常执行结束后释放锁，一个是在代码执行异常时释放锁。

再看下 synchronized 修饰方法有哪些区别：

![image-20210104175004307](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104175004307.png)

查看字节码：

![image-20210104175053484](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104175053484.png)

从图中可以看出，被 synchronized 修饰的方法在被编译为字节码后，在**方法的 flags 属性中会被标记为 ACC_SYNCHRONIZED 标志**。当虚拟机访问一个被标记为 ACC_SYNCHRONIZED 的方法时，会自动在方法的开始和结束（或异常）位置添加 monitorenter 和 monitorexit 指令。

关于 monitorenter 和 monitorexit，可以理解为一把具体的锁。在这个锁中保存着两个比较重要的属性：**计数器和指针**。

- 计数器代表当前线程一共访问了几次这把锁；

- 指针指向持有这把锁的线程。


用一张图表示如下：

![img](https://s0.lgstatic.com/i/image3/M01/89/8E/Cgq2xl6X-COAEskYAABd1Qkprak432.png)

锁计数器默认为0，当执行monitorenter指令时，如锁计数器值为 0 说明这把锁并没有被其它线程持有。那么这个线程会将计数器加1，并将锁中的指针指向自己。当执行monitorexit指令时，会将计数器减1。

# 三、ReentrantLock

## 1、ReentrantLock 基本使用

ReentrantLock 的使用同 synchronized 有点不同，它的**加锁和解锁操作都需要手动完成**，如下所示：

![image-20210312104518516](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210312104518516.png)

图中 lock() 和 unlock() 分别是加锁和解锁操作。运行效果如下：

![image-20210312104540476](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210312104540476.png)

可以看出，使用 ReentrantLock 也能实现同 synchronized 相同的效果。

**那如果我们忘记解锁呢？**

![image-20210312104704097](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210312104704097.png)

此时，t2会一直等待锁的释放。

> 因为 ReentrantLock 与 synchronized 不同，当方法结束或者异常发生时 synchronized 会自动释放锁，但是 ReentrantLock 并不会自动释放锁。因此好的方式是将 unlock 操作放在 finally 代码块中，保证任何时候锁都能够被正常释放掉。 如下：

![image-20210312105506012](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210312105506012.png)

## 2、公平锁实现

ReentrantLock 有一个带参数的构造器，如下：

![img](https://s0.lgstatic.com/i/image3/M01/89/8E/Cgq2xl6X-CSAAdUqAACzsvj2pFg758.png)

**默认情况下，synchronized 和 ReentrantLock 都是非公平锁。**但是 ReentrantLock 可以通过传入 true 来创建一个公平锁。所谓**公平锁就是通过同步队列来实现多个线程按照申请锁的顺序获取锁。**

公平锁使用实例如下：

![image-20210312111125326](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210312111125326.png)

运行效果如下：

![image-20210312111100028](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210312111100028.png)

可以看出，创建的 3 个线程依次按照顺序去修改 sharedNumber 的值。

那如果上面这个例子中我们使用的不是公平锁呢？

![image-20210312111408587](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210312111408587.png)

结果如下：

![image-20210312111438951](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210312111438951.png)![image-20210312111456646](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210312111456646.png)

可以看到，线程获得锁的顺序是是不确定的。

> 网上对公平锁有一段例子很经典：假设有一口水井，有管理员看守，管理员有一把锁，只有拿到锁的人才能够打水，打完水要把锁还给管理员。每个过来打水的人都要得到管理员的允许并拿到锁之后才能去打水，如果前面有人正在打水，那么这个想要打水的人就必须排队。管理员会查看下一个要去打水的人是不是队伍里排最前面的人，如果是的话，才会给你锁让你去打水；如果你不是排第一的人，就必须去队尾排队，这就是公平锁。

## 四、读写锁（ReentrantReadWriteLock）

在开发中，我们经常会定义一个线程间共享的用作缓存的数据结构。比如一个较大的 Map，缓存中保存了全部的城市 Id 和城市 name 对应关系。这个大 Map 绝大部分时间提供读服务（根据城市 Id 查询城市名称等）。而写操作占有的时间很少，通常是在服务启动时初始化，然后可以每隔一定时间再刷新缓存的数据。但是**写操作开始到结束之间，不能再有其他读操作进来，并且写操作完成之后的更新数据需要对后续的读操作可见。**

在没有读写锁支持的时候，如果想要完成上述工作就需要使用 Java 的等待通知机制，就是当写操作开始时，所有晚于写操作的读操作均会进入等待状态，只有写操作完成并进行通知之后，所有等待的读操作才能继续执行。这样做的目的是使读操作能读取到正确的数据，不会出现脏读。

但是如果使用 concurrent 包中的读写锁（ReentrantReadWriteLock）实现上述功能，就只需要在**读操作时获取读锁，写操作时获取写锁**即可。**当写锁被获取到时，后续的读写锁都会被阻塞，写锁释放之后，所有操作继续执行**，这种编程方式相对于使用等待通知机制的实现方式而言，变得简单明了。

接下来，我们看下读写锁（ReentrantReadWriteLock）如何使用：

1. 创建读写锁对象：

![image-20210312141202503](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210312141202503.png)

2. 通过读写锁对象分别获取读锁（ReadLock）和写锁（WriteLock）：

![image-20210312141317193](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210312141317193.png)

3. 使用读锁（ReadLock）同步缓存的读操作，使用写锁（WriteLock）同步缓存的写操作：

![image-20210312141737999](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210312141737999.png)

![image-20210312141832937](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210312141832937.png)

## 1、示例

```java
public class ReentrantReadWriteLockTest {
    private static ReentrantReadWriteLock mLock = new ReentrantReadWriteLock();
    // 缓存数据
    private static int sharedNum = 0;

    public static void main(String[] args) {
        Thread t1 = new Thread(new Reader(), "Reader  1");
        Thread t2 = new Thread(new Reader(), "Reader  2");
        Thread t3 = new Thread(new Writer(), "Writer  3");

        t1.start();
        t2.start();
        t3.start();
    }

    static class Reader implements Runnable {
        @Override
        public void run() {
            for (int i = 0; i < 10; i++) {
                try {
                    // 模拟每隔 0.5s 读一次数据
                    Thread.sleep(500L);
                    mLock.readLock().lock();
                    System.out.println(Thread.currentThread().getName() + "--> Num is " + sharedNum);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    mLock.readLock().unlock();
                }
            }
        }
    }

    static class Writer implements Runnable {
        @Override
        public void run() {
            for (int i = 0; i < 5; i++) {
                try {
                    // 模拟 每隔 1s 写一次数据
                    Thread.sleep(1000L);
                    mLock.writeLock().lock();
                    sharedNum = i;
                    System.out.println(Thread.currentThread().getName() + "--> 正在写入 " + sharedNum);
                } catch (Exception e) {
                } finally {
                    mLock.writeLock().unlock();
                }
            }
        }
    }
}
```

执行结果：

![image-20210312142040406](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210312142040406.png)

可以看出当写入操作在执行时，读取数据的操作会被阻塞。当写入操作执行成功后，读取数据的操作继续执行，并且读取的数据也是最新写入后的实时数据。

# 五、总结

这次学习了 Java 中两个实现同步的方式 **synchronized** 和 **ReentrantLock**。

synchronized 使用更简单，加锁和释放锁都是由虚拟机自动完成，而 ReentrantLock 需要手动去完成。

ReentrantLock 的使用场景更多，**公平锁**还有**读写锁**都可以在复杂场景中发挥重要作用。