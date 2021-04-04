#一、Handler是什么？
###1、Handler定义
关于Handler是什么，网上的博客很多，我觉得还是官方说的最具概括性：
> A Handler allows you to send and process {@link Message} and Runnable objects associated with a thread's {@link MessageQueue}.  Each Handler instance is associated with a single thread and that thread's message queue.  When you create a new Handler, it is bound to the thread / message queue of the thread that is creating it -- from that point on, it will deliver messages and runnables to that message queue and execute them as they come out of the message queue.

翻译：
1、Handler是用来发送和处理（与线程的MessageQueue相关联的）Message和Runnable objects的。
2、每个Handler实例与单个线程以及此线程的message queue相关联。
3、从new一个Handler的时候开始，这个Handler就与创建它的线程以及此线程的message queue绑定起来了。
4、它（Handler）会传递（添加）messages和runnables到message queue中，并且会在它们从message queue中取出的时候执行它们。

###2、Handler主要用处
>There are two main uses for a Handler: (1) to schedule messages and runnables to be executed as some point in the future; and (2) to enqueue an action to be performed on a different thread than your own.

小结：
Handler的主要作用有两点：
（1）添加任务（messages and runnables）在将来执行
（2）添加任务到不同的线程执行

#二、Handler实例的创建与获取
###1、Handler实例的创建依靠下列构造方法：
```
public class Handler {
    public Handler() {
        this(null, false);
    }

    public Handler(Callback callback) {
        this(callback, false);
    }

    public Handler(Looper looper) {
        this(looper, null, false);
    }

    public Handler(Looper looper, Callback callback) {
        this(looper, callback, false);
    }

    public Handler(boolean async) {
        this(null, async);
    }

    public Handler(Callback callback, boolean async) {
        if (FIND_POTENTIAL_LEAKS) {
            final Class<? extends Handler> klass = getClass();
            if ((klass.isAnonymousClass() || klass.isMemberClass() || klass.isLocalClass()) &&
                    (klass.getModifiers() & Modifier.STATIC) == 0) {
                Log.w(TAG, "The following Handler class should be static or leaks might occur: " +
                    klass.getCanonicalName());
            }
        }

        mLooper = Looper.myLooper();
        if (mLooper == null) {
            throw new RuntimeException(
                "Can't create handler inside thread " + Thread.currentThread()
                        + " that has not called Looper.prepare()");
        }
        mQueue = mLooper.mQueue;
        mCallback = callback;
        mAsynchronous = async;
    }

    public Handler(Looper looper, Callback callback, boolean async) {
        mLooper = looper;
        mQueue = looper.mQueue;
        mCallback = callback;
        mAsynchronous = async;
    }

    final Looper mLooper;
    final MessageQueue mQueue;
    final Callback mCallback;
    final boolean mAsynchronous;
}
```
分析：
######（1）Handler的构造方法这么多，其本质是什么？
创建Handler的时候都会初始化它的四个变量：
```
    final Looper mLooper;
    final MessageQueue mQueue;
    final Callback mCallback;
    final boolean mAsynchronous;
```
这四个变量都有final修饰，一旦赋值，以后就不能再改变了。

因此创建Handler的时候就把Handler与Looper以及Looper持有的MessageQueue绑定起来了，进而绑定了对应的线程。
这里先简单分析下Handler、Looper、MessageQueue、Thread之间的关系：
Handler持有Looper的引用 -> Looper持有当前thread的引用，并且创建了（线程唯一）一个MessageQueue。
因此，也就绑定了一种关系：一个Handler对应一个Looper，进而对应一个线程，对应一个MessageQueue（为什么对应一个Looper就对应一个线程和一个MessageQueue在Looper源码分析中再说）。

######（2）下面分析这四个变量各自的作用
i、mLooper：绑定Handler与Looper。
ii、mQueue：绑定Handler与MessageQueue，之后发送的消息和Runnables就是添加到MessageQueue中。
iii、mCallback：会影响到消息的处理流程：如果mCallback != null，则会调用mCallback.handleMessage(msg)方法，并根据返回结果决定是否调用handleMessage(msg);方法。
```
    public void dispatchMessage(Message msg) {
        if (msg.callback != null) {
            handleCallback(msg);
        } else {
            if (mCallback != null) {
                if (mCallback.handleMessage(msg)) {
                    return;
                }
            }
            handleMessage(msg);
        }
    }
```
iv、mAsynchronous：
```
    private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
        msg.target = this;
        if (mAsynchronous) {
            msg.setAsynchronous(true);
        }
        return queue.enqueueMessage(msg, uptimeMillis);
    }
```

######（3）Looper参数
如果不传入Looper参数，则默认：
```
mLooper = Looper.myLooper();
```
获取的是当前线程对应的Looper。

注：关于Looper是如何获取到当前线程的，请看：Handler消息机制之Looper类。

###2、Handler实例的获取使用下列方法：
```
public class Handler {
    public static Handler createAsync(@NonNull Looper looper) {
        if (looper == null) throw new NullPointerException("looper must not be null");
        return new Handler(looper, null, true);
    }

    public static Handler createAsync(@NonNull Looper looper, @NonNull Callback callback) {
        if (looper == null) throw new NullPointerException("looper must not be null");
        if (callback == null) throw new NullPointerException("callback must not be null");
        return new Handler(looper, callback, true);
    }

    public static Handler getMain() {
        if (MAIN_THREAD_HANDLER == null) {
            MAIN_THREAD_HANDLER = new Handler(Looper.getMainLooper());
        }
        return MAIN_THREAD_HANDLER;
    }

    public static Handler mainIfNull(@Nullable Handler handler) {
        return handler == null ? getMain() : handler;
    }
}
```
这四个方法最终也是通过构造方法创建并获取。

#三、Handler的使用之sendMessage。
###1、send消息的整体流程图：
![发送消息流程图](https://upload-images.jianshu.io/upload_images/9000209-edde8f6197935043.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###2、源码分析：
1、所有send出去的消息最终都通过enqueueMessage()添加到了messageQueue中：
```
    public final boolean sendMessage(Message msg)
    {
        return sendMessageDelayed(msg, 0);
    }

    public final boolean sendMessageDelayed(Message msg, long delayMillis)
    {
        if (delayMillis < 0) {
            delayMillis = 0;
        }
        return sendMessageAtTime(msg, SystemClock.uptimeMillis() + delayMillis);
    }

    public boolean sendMessageAtTime(Message msg, long uptimeMillis) {
        MessageQueue queue = mQueue;
        if (queue == null) {
            RuntimeException e = new RuntimeException(
                    this + " sendMessageAtTime() called with no mQueue");
            Log.w("Looper", e.getMessage(), e);
            return false;
        }
        return enqueueMessage(queue, msg, uptimeMillis);
    }

    private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
        msg.target = this;
        if (mAsynchronous) {
            msg.setAsynchronous(true);
        }
        return queue.enqueueMessage(msg, uptimeMillis);
    }
```
默认情况下，uptimeMillis = SystemClock.uptimeMillis()+delayMillis。
```
//SystemClock.uptimeMillis():开机到现在的时间（不包含睡眠时间）
return sendMessageAtTime(msg, SystemClock.uptimeMillis() + delayMillis);
```

2、值得注意的点有：
* sendMessageAtTime()方法并没有final修饰，意味着子类可重写。

3、sendEmptyMessage(int what)系列方法，就是Handler默认创建了一个Message并指定了message.what。

#三、Handler的使用之postMessage。
###1、整体概述
post系列的方法都会传入一个Runnable对象，而这个Runnable对象会通过getPostMessage方法设为m.callback的值（m为Message，也是自动创建的）。
###2、源码分析
######1）post方法，其实内部都是调用的send方法。只是，在中间调用了getPostMessage方法为Message指定了callback。
```
    public final boolean post(Runnable r)
    {
       return  sendMessageDelayed(getPostMessage(r), 0);
    }

    public final boolean postAtTime(Runnable r, long uptimeMillis)
    {
        return sendMessageAtTime(getPostMessage(r), uptimeMillis);
    }

    public final boolean postAtTime(Runnable r, Object token, long uptimeMillis)
    {
        return sendMessageAtTime(getPostMessage(r, token), uptimeMillis);
    }

    public final boolean postDelayed(Runnable r, long delayMillis)
    {
        return sendMessageDelayed(getPostMessage(r), delayMillis);
    }

    public final boolean postDelayed(Runnable r, Object token, long delayMillis)
    {
        return sendMessageDelayed(getPostMessage(r, token), delayMillis);
    }

    public final boolean postAtFrontOfQueue(Runnable r)
    {
        return sendMessageAtFrontOfQueue(getPostMessage(r));
    }
```
######2）getPostMessage(Runnable r)和getPostMessage(Runnable r, Object token)
```
    private static Message getPostMessage(Runnable r) {
        Message m = Message.obtain();
        m.callback = r;
        return m;
    }

    private static Message getPostMessage(Runnable r, Object token) {
        Message m = Message.obtain();
        m.obj = token;
        m.callback = r;
        return m;
    }
```

######3）post的几个方法还可以传入一个token对象，这个是被设为了message.obj。被token标记的message是可以被统一移除的。
```
    /**
     * Remove any pending posts of Runnable <var>r</var> with Object
     * <var>token</var> that are in the message queue.  If <var>token</var> is null,
     * all callbacks will be removed.
     */
    public final void removeCallbacks(Runnable r, Object token)
    {
        mQueue.removeMessages(this, r, token);
    }
```
源码说明：移除所有token标记的message，如果token为null，移除所有callbacks。

###3、message.calback。
这个值是会对消息分发产生影响的：
```
    public void dispatchMessage(Message msg) {
        if (msg.callback != null) {
            handleCallback(msg);
        } else {
            if (mCallback != null) {
                if (mCallback.handleMessage(msg)) {
                    return;
                }
            }
            handleMessage(msg);
        }
    }

    private static void handleCallback(Message message) {
        message.callback.run();
    }
```

分析：
1、如果msg.callback != null的话，最终分发msg给Handler的时候就只会执行handleCallback方法了。

四、Handler的使用之obtainMessage。
使用obtainMessage方法获取Message时，内部都是使用了Message.obtain。我们需要注意的一个点是：在调用Message.obtain方法时，都把当前的Handler作为参数传进去了，然后被赋值给message.target。
1、源码
```
    public final Message obtainMessage()
    {
        return Message.obtain(this);
    }

    public final Message obtainMessage(int what)
    {
        return Message.obtain(this, what);
    }
    
    public final Message obtainMessage(int what, Object obj)
    {
        return Message.obtain(this, what, obj);
    }

    public final Message obtainMessage(int what, int arg1, int arg2)
    {
        return Message.obtain(this, what, arg1, arg2);
    }
  
    public final Message obtainMessage(int what, int arg1, int arg2, Object obj)
    {
        return Message.obtain(this, what, arg1, arg2, obj);
    }
```

#五、removeCallbacks和removeMessages
移除指定的message。可以指定what，runnable，token。如果token == null，则移除所有message。
```
    public final void removeCallbacks(Runnable r)
    {
        mQueue.removeMessages(this, r, null);
    }

    public final void removeCallbacks(Runnable r, Object token)
    {
        mQueue.removeMessages(this, r, token);
    }

    public final void removeMessages(int what) {
        mQueue.removeMessages(this, what, null);
    }

    public final void removeMessages(int what, Object object) {
        mQueue.removeMessages(this, what, object);
    }

    public final void removeCallbacksAndMessages(Object token) {
        mQueue.removeCallbacksAndMessages(this, token);
    }
```

六、总结
此篇博客主要是过一遍Handler的源码，看了Handler的构造方法、post方法、send方法、remove方法。
还有一些方法没有分析，先看完Looper源码后，再来分析剩下的，比如dispatchMessage等，还有一些不常用的。