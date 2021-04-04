2020.08.26

#一、为什么要学Handler？
1、不能在子线程更新UI
2、Handler使用不当可能引起的内存泄露
3、Message的优化
4、在子线程中创建Handler，需要为这个Handler准备Looper
5、在Handler处理完消息后，但页面已经销毁了，这时候Handler更新UI，可能出现View的资源引用不见了，就会出现NullPointer Exception。

#二、Handler整体架构
1、Handler能做什么？
1）处理延时任务（设定将来某个时间处理某个事情）
推送未来某个时间点将要执行的Message或者Runnable到消息队列。
2）线程间通信（主线程和子线程的通信）
在子线程把需要在另一个线程执行的操作加入到消息队列中。

2、 Handler模型——传送带
![Handler模型](https://upload-images.jianshu.io/upload_images/9000209-a8c115853ecc505c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个模型展示了Handler核心构成（Message、MessageQueue、Thread、Looper）的关系。

#三、Handler源码整体分析
1）handler.sendMessage()---->发送消息----->MessageQueue.enqueueMessage()

![Handler#sendMessage主要方法](https://upload-images.jianshu.io/upload_images/9000209-2f783dd93afd0ac6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2）往MessageQueue中添加消息
每一条被添加的Message，在添加的时候都伴有一个时间，这个时间将作为入列位置的依据
```
boolean enqueueMessage(Message msg, long when) {}
```
![image.png](https://upload-images.jianshu.io/upload_images/9000209-c24ca153aba7ed15.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3）从MessageQueue中取出消息
MessageQueue#next() ：返回、销毁队列里面的消息
```
Message next() {}
```
从消息队列中获取消息，使用for循环

4）Looper#loop()
第一步：调用MessageQueue#next()取出消息。
第二步：分发消息到Handler
```
public static void loop() {
        final Looper me = myLooper();
        ...
        final MessageQueue queue = me.mQueue;
        ...
        for (;;) {
            Message msg = queue.next(); // might block
            ...
            msg.target.dispatchMessage(msg);
            ...
        }
}
```

5）Looper#loop()在哪调用的？
ActivityThread#main():
```
public static void main(String[] args) {
    ...
    Looper.prepareMainLooper();
    ...
    Looper.loop();
    ...
}
```

源码分析：
1、MessageQueue来自何处？
一直在说往MessageQueue中添加、取出消息，那MessageQueue来自何处？
```
public final class Looper {
...
    final MessageQueue mQueue;
...
    private Looper(boolean quitAllowed) {
        mQueue = new MessageQueue(quitAllowed);
        mThread = Thread.currentThread();
    }
...
}
```
因此，得出结论：
Handler发送消息，就是往Looper中的MessageQueue添加消息。
Looper从MessageQueue中取出消息并分发给Handler。

#四、源码细节分析


#五、为什么Handler能够跨线程通信
![image.png](https://upload-images.jianshu.io/upload_images/9000209-1ab216c379c52b15.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

MessageQueue在内存中是唯一的。

入队：根据时间排序，当队列满（思考：何时满？）的时候，阻塞，直到用户通过next取出消息。（next何时调用？怎么控制的？）
当next方法被调用，通知MessageQueue可以进行消息入队。

出队：由Looper.loop()，启动轮询器，对queue进行轮询。当消息达到执行时间就取出来。当message queue为空的时候，队列阻塞，等消息队列调用enqueueMessage的时候，通知队列，可以取出消息了，停止阻塞。

Handler是怎么实现消息阻塞的？

相关问题总结：
![image.png](https://upload-images.jianshu.io/upload_images/9000209-db79cd0bae72d5ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)