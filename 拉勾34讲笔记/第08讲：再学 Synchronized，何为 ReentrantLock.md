本文将对 synchronized 关键字和 ReentrantLock 做一个详细的比较。



### synchronized

synchronized 可以用来修饰以下 3 个层面：

1. 修饰实例方法；

2. 修饰静态类方法；

3. 修饰代码块。


#### synchronized 修饰实例方法

![image-20210104171100957](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104171100957.png)

这种情况下的锁对象是当前实例对象，因此只有同一个实例对象调用此方法才会产生互斥效果，不同实例对象之间不会有互斥效果。比如如下代码：

![image-20210104171846338](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104171846338.png)

上述代码，在不同的线程中调用的是不同对象的 printLog 方法，因此彼此之间不会有排斥。运行效果如下：

![image-20210104172102512](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104172102512.png)

可以看出，两个线程是交互执行的。

如果将代码进行如下修改，两个线程调用同一个对象的 printLog 方法：

![image-20210104172224988](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104172224988.png)

则执行效果如下：

![image-20210104172301131](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104172301131.png)

可以看出：只有某一个线程中的代码执行完之后，才会调用另一个线程中的代码。也就是说此时两个线程间是互斥的。

#### 修饰静态类方法

如果 synchronized 修饰的是静态方法，则锁对象是当前类的 Class 对象。因此即使在不同线程中调用不同实例对象，也会有互斥效果。

将 SynchronizedMehtods 中的 printLog 修改为静态方法，如下：

![image-20210104172458909](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104172458909.png)

执行后的打印效果如下：

![image-20210104172626694](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104172626694.png)

可以看出，两个线程还是依次执行的。

#### synchronized 修饰代码块

除了直接修饰方法之外，synchronized 还可以作用于代码块，如下代码所示：

![image-20210104173719274](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104173719274.png)

synchronized 作用于代码块时，**锁对象就是跟在后面括号中的对象**。上图中可以看出任何 Object 对象都可以当作锁对象。

执行结果如下：

![image-20210104173810420](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104173810420.png)

### 实现细节

synchronized 既可以作用于方法，也可以作用于某一代码块。但在实现上是有区别的。 比如如下代码，使用 synchronized 作用于代码块：

![image-20210104174454192](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104174454192.png)

使用 javap 查看上述 test1 方法的字节码，可以看出，编译而成的字节码中会包含 monitorenter 和 monitorexit 这两个字节码指令。如下所示：

![image-20210104174841765](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104174841765.png)

可以发现，上面字节码中有 1 个 monitorenter 和 2 个 monitorexit。这是因为虚拟机需要保证当异常发生时也能释放锁。因此 2 个 monitorexit 一个是代码正常执行结束后释放锁，一个是在代码执行异常时释放锁。

再看下 synchronized 修饰方法有哪些区别：

![image-20210104175004307](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104175004307.png)

查看字节码：

![image-20210104175053484](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210104175053484.png)

从图中可以看出，被 synchronized 修饰的方法在被编译为字节码后，在方法的 flags 属性中会被标记为 ACC_SYNCHRONIZED 标志。当虚拟机访问一个被标记为 ACC_SYNCHRONIZED 的方法时，会自动在方法的开始和结束（或异常）位置添加 monitorenter 和 monitorexit 指令。

关于 monitorenter 和 monitorexit，可以理解为一把具体的锁。在这个锁中保存着两个比较重要的属性：计数器和指针。

- 计数器代表当前线程一共访问了几次这把锁；

- 指针指向持有这把锁的线程。


用一张图表示如下：

![img](https://s0.lgstatic.com/i/image3/M01/89/8E/Cgq2xl6X-COAEskYAABd1Qkprak432.png)

锁计数器默认为0，当执行monitorenter指令时，如锁计数器值为0 说明这把锁并没有被其它线程持有。那么这个线程会将计数器加1，并将锁中的指针指向自己。当执行monitorexit指令时，会将计数器减1。



### ReentrantLock

#### ReentrantLock 基本使用

ReentrantLock 的使用同 synchronized 有点不同，它的加锁和解锁操作都需要手动完成，如下所示：

