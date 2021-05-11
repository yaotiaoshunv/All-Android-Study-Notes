> 知识路径：Android > 四大组件 > Activity
>
> version：2021/4/14
>
> review：2020/09/07---2021/4/14
>
> 掌握程度：初学



## 一、前言

![image-20210414161259362](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210414161259362.png)

## 二、Activity 的生命周期

### 2.1 Activity 的 4 种状态

**Active/Paused/Stopped/Killed**

**Activie：**当前 Activity 正处于运行状态，指的是当前 Activity 获取了焦点。

**Paused：**当前 Activity 正处于暂停状态，指的是当前 Activity 失去焦点，此时的 Activity

并没有被销毁，内存里面的成员变量、状态信息等仍然存在，当然这个 Activity 也仍然可

见，但是焦点却不在它身上，比如被一个对话框形式的 Activity 获取了焦点，或者被一个

透明的 Activity 获取了焦点，这都能导致当前的 Activity 处于 paused 状态。

**Stopped：**与 paused 状态相似，stopped 状态的 Activity 是完全不可见的，但是内存里

面的成员变量、状态信息等仍然存在，但是也没有被销毁。

**Killed：**已经被销毁的 Activity 才处于 killed 状态，它的内存里面的成员变量、状态信息等

都会被一并回收。

### 2.2 Activity 的生命周期分析

#### 2.2.1 正常情况下的生命周期

Activity 启动 –> onCreate()–>onStart()–>onResume()

点击 home 键回到桌面 –> onPause()–>onStop()

再次回到原 Activity –> onRestart()–>onStart()–>onResume()

退出当前 Activity 时 –> onPause()–>onStop()–>onDestroy()

**详细生命周期如下：**

![image-20210414141633715](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210414141633715.png)

1、启动了一个 Activity，通常是 Intent 来完成。启动一个 Activity 首先要执行的回调函数是onCreate()。通常在代码中你需要在此函数中做一些初始化的工作，比如绑定布局，绑定控件，初始化数据等。

2、即将执行 Activity 的 onStart() 函数，执行之后 Activity 已经可见，但是还没有出现在前台，无法与用户进行交互。这个时候通常 Activity 已经在后台准备好了，但是就差执行 onResume() 函数出现在前台。

3、即将执行 Activity 的 onResume()函数，执行之后 Activity 不止可见而且还会出现在前台，可以与用户进行交互啦。

4、由于 Activity 执行了 onResume() 函数，所以 Activity 出现在了前台。也就是 Activity 处于运行状态。

5、处于运行状态的 Activity 即将执行 onPause() 函数，什么情况下促使 Activity 执行 onPause() 方法呢？

​	[1] 启动了一个新的 Activity

​	[2] 返回上一个 Activity

可以理解为当需要其他 Activity，当前的 Activity 必须先把手头的工作暂停下来，再来把当前的界面空间交给下一个需要界面的 Activity，而 onPause() 方法可以看作是一个转接工作的过程，因为屏幕空间只有那么一个，每次只允许一个 Activity 出现在前台进行工作。通常情况下 onPause() 函数不会被单独执行，执行完 onPause() 方法后会继续执行 onStop() 方法，执行完 onStop() 方法才真正意味着当前的 Activity 已经退出前台，存在于后台。

6、Activity 即将执行 onStop() 函数，在“5”中已经说得很清楚了，当 Activity 要从前台切换至后台的时候会执行，比如：用户点击了返回键，或者用户切换至其他 Activity 等。

7、当前的 Activity 即将执行 onDestory() 函数，代表着这个 Activity 即将进入生命的终结点，这是 Activity 生命周期中的最后一次回调生命周期，我们可以在 onDestory() 函数中进行一些回收工作和资源的释放工作，比如：广播接收器的注销等。

8、执行完 onDestory() 方法的 Activity 接下来面对的是被 GC 回收，宣告生命终结。

9、很少情况下 Activity 才走“9”，网上一些关于对话框弹出后 Activity 会走“9”的说法，经过笔者验证，在某个 Activity 内弹出对话框并没有走“9”，所以网上大部分这样说法的文章要么是没验证，要么直接转载的，这个例子说明，实验出真知，好了，不废话了，那么什么情况下，Activity 会走“9”呢？

10、当用户在其他的 Activity 或者桌面回切到这个 Activity 时，这个 Activity 就会先去执行onRestart() 函数，Restart 有“重新开始”的意思，然后接下来执行 onStart() 函数，接着执行 onResume() 函数进入到运行状态。

11、在“10”中讲的很清楚了。

12、高优先级的应用急需要内存，此时处于低优先级的此应用就会被 kill 掉。

13、用户返回原 Activity。

**下面来着重说明一下 Activity 每个生命周期函数：**

**onCreate()：**表示 Activity 正在被创建，这是 Activity 生命周期的第一个方法。通常我们要在此函数中做初始化的工作，比如：绑定布局、控件，初始化数据等。

**onStart()：**表示 Activity 正在被启动，这时候的 Activity 已经被创建好了，完全过了准备阶段，但是没有出现在前台，需要执行 onResume()函数才可以进入到前台与用户进行交互。

onResume()：表示 Activitiy 已经可见了，并且 Activity 处于运行状态，也就是 Activity 不止出现在了前台，而且还可以让用户点击，滑动等等操作与它进行交互。

onPause()：表示 Activity 正在暂停，大多数情况下，Activity 执行完 onPause() 函数后会继续执行 onStop() 函数，造成这种函数调用的原因是当前的 Activity 启动了另外一个 Activity 或者回切到上一个 Activity。还有一种情况就是 onPause() 函数被单独执行了，并没有附带执行 onStop() 方法，造成这种函数调用的原因很简单，就是当前 Activity 里启动了类似于对话框的东西或者透明的Activity。

onStop()：表示 Activity 即将停止，我们应该在此函数中做一些不那么耗时的轻量级回收操作。

onRestart()：表示 Activity 正在重新启动。一般情况下，一个存在于后台不可见的 Activity 变为可见状态，都会去执行 onRestart() 函数，然后会继续执行 onStart() 函数，onResume() 函数出现在前台并且处于运行状态。

onDestory()：表示 Activity 要被销毁了。这是 Activity 生命中的最后一个阶段，我们可以在此函数中做一些回收工作和资源释放等，比如：广播接收器的注销等。

#### 2.2.2 异常情况下的生命周期

看完了正常情况下的生命周期，接下来看一下异常情况，首先要知道：

**什么是异常情况？**

情况 1：资源相关的系统配置发生改变导致 Activity 被杀死并重新创建。

![image-20210414144335857](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210414144335857.png)

可以从图中看出当 Activity 发生意外的情况的时候，这里的意外指的就是**系统配置发生改变**，Activity 会被销毁，其 onPause、OnStop、onDestory 函数均会被调用，同时由于Activity 是在异常情况下终止的，系统会调用 onSaveInstanceState 来保存当前 Activity 状态。调用onSaveInstanceState 的时机总会发生在 onStop 之前，至于会不会调用时机发生在 onPause 方法之前，那就说不定了，这个没有固定的顺序可言，正常情况下一般 onSaveInstanceState 不会被调用。当 Activity 被重新创建后，系统会调用 onRestoreInstanceState，并且把 Activity 销毁时 onSaveInstanceState 方法所保存的 Bundle 对象作为参数传递给 onRestoreInstanceState 和 onCreate 方法。所以我们可以通过 onRestoreInstanceState 和 onCreate 方法来判断 Activity 是否被重建了，如果被重建了，那么我们就可以取出之前保存的数据并恢复，从时序上来看，onRestoreInstanceState 的调用时机发生在 onStart 之后。同时，在 onSaveInstanceState 和 onRestoreInstanceState 方法中，系统自动为我们做了一定的恢复工作。当 Activity 在异常情况下需要重新创建时，系统会默认为我们保存当前 Activity 的视图结构，并且在 Activity 重启后为我们恢复这些数据，比如：文本框中用户输入的数据，ListView 滚动的位置等，这些 View 相关的状态系统都能够默认为我们恢复。具体针对某一个特定的 View 系统能为我们恢复哪些数据，我们可以查看 View 的源码。和 Activity 一样，每个 View 都有 onSaveInstanceState 和onRestoreInstanceState 这两个方法，看一下它们的具体实现，就能知道系统能够自动为每个 View 恢复哪些数据。

**关于保存和恢复 View 层次结构，系统的工作流程是这样的：**

首先 Activity 被意外终止时，Activity 会调用 onSaveInstanceState 去保存数据，然后 Activity 会委托 Window 去保存数据，接着 Window 再委托它上面的顶级容器去保存数据。顶级容器是一个 ViewGroup，一般来说它很可能是 DecorView。最后顶层容器再去一一通知它的子元素来保存数据，这样整个数据保存过程就完成了。可以发现，这是一个典型的委托思想，上层委托下层，父容器去委托子元素去处理一件事情，这种思想在 Android 中有很多应用，比如：View 的绘制过程，事件分发等都是采用类似的思想。至于数据恢复过程也是类似的，这样就不再重复介绍了。

情况 2：资源内存不足导致低优先级的 Activity 被杀死。

首先，Activity 有优先级？你肯定怀疑，代码中都没设置过啊！优先级从何而来，其实这里的 Activity 的优先级是指一个 Activity 对于用户的重要程度，比如：正在与用户进行交互的 Activity 那肯定是最重要的。我们可以按照重要程度将 Activity 分为以下等级：

优先级最高： 与用户正在进行交互的 Activity，即前台 Activity。

优先级中等：可见但非前台的 Activity，比如：一个弹出对话框的 Activity，可见但是非前台运行。

优先级最低：完全存在于后台的 Activity，比如：执行了 onStop。

当内存严重不足时，系统就会按照上述优先级去 kill 掉目前 Activity 所在的进程，并在后续通过 onSaveInstanceState 和 onRestoreInstanceState 来存储和恢复数据。如果一个进程中没有四大组件的执行，那么这个进程将很快被系统杀死，因此，一些后台工作不适合脱离四大组件独立运行在后台中，这样进程更容易被杀死。比较好的方法就是将后台工作放入 Service 中从而保证进程有一定的优先级，这样就不会轻易地被系统杀死。

总结：

上面分析了系统的数据存储和恢复机制，我们知道，当系统配置发生改变之后，Activity会被重新创建，那么有没有办法不重新创建呢？答案是有的，接下来我们就来分析这个问题。系统配置中有很多内容，如果某项内容发生了改变后，我们不想系统重新创建 Activity，那可以给 Activity 指定 configChanges 属性。比如我们不想让 Activity 在屏幕旋转的时候重新创建，就可以给configChanges 属性添加一些值，请继续往下看。

### 2.3 一些特殊情况下的生命周期分析

#### 2.3.1 Activity 的横竖屏切换（最好再验证一下）

与横竖屏生命周期函数调用有关的属性是"android:configChanges"，关于它的属性值设置影响如下：

- orientation：消除横竖屏的影响
- keyboardHidden：消除键盘的影响
- screenSize：消除屏幕大小的影响

下面我们来看一下横竖屏切换时的生命周期流程：

情况一：当 Activity 的 android:configChanges 属性不设置的时候：

```java
1）启动Activity
onCreate --> 
onStart --> 
onResume

2）切换为横屏
onSaveInstanceState --> 
onPause -->
onStop -->
onDestroy-->
onCreate -->
onStart --> 
onRestoreInstanceState --> 
onResume

3）再次切换为竖屏，执行了两次
onSaveInstanceState-->
onPause-->
onStop-->
onDestroy-->
onCreate-->
onStart-->
onRestoreInstanceState-->
onResume-->

onSaveInstanceState-->
onPause-->
onStop-->
onDestroy-->
onCreate-->
onStart-->
onRestoreInstanceState-->
onResume
```

> 有的手机只会调用一次，应该是手机厂商对源码进行了修改。不同手机、Android版本应该有不同的回调方式。
>
> 我用vivoX6Plus-Android5.1.1测试，只会执行一次流程。  

情况二：当我们设置 Activity 的 android:configChanges 属性为 orientation，它的生命周期会走如下流程：

```java
4）切换为横屏
onSaveInstanceState-->
onPause-->
onStop-->
onDestroy-->
onCreate-->
onStart-->
onRestoreInstanceState-->
onResume

5）再切换为竖屏，此时不会执行两次生命周期，但是多了一个onConfigurationChanged
onSaveInstanceState-->
onPause-->
onStop-->
onDestroy-->
onCreate-->
onStart-->
onRestoreInstanceState-->
onResume-->
onConfigurationChanged
```

情况三：Activity 的 android:configChanges 属性为 orientation|keyboardHidden 时：

切屏不会重新调用各个生命周期，只会执行onConfigurationChanged方法。

> 有的版本还需要加上 screenSize 才是此效果。即orientation|screenSize|keyboardHidden

总结：

1、不设置Activity的android:configChanges时，切屏会重新调用各个生命周期，
切横屏时会执行一次，切竖屏时会执行两次
2、设置Activity的android:configChanges="orientation"时，切屏还是会重新调
用各个生命周期，切横、竖屏时只会执行一次
3、设置Activity的android:configChanges="orientation|keyboardHidden"时，
切屏不会重新调用各个生命周期，只会执行onConfigurationChanged方法

##### 2.3.1.1 onConfigurationChanged

当Configuration改变后，ActivityManagerService将会发送"配置改变"的广播，会要求ActivityThread 重新启动当前focus的 Activity，这是默认情况，我们不做任何处理。如果我们用android:configChanges来配置 Activity 信息，那么就可以避免对 Activity 销毁再重新创建，而是调用onConfigurationChanged方法。

onConfigurationChanged方法一般与android:configChanges属性成双成对，android:configChanges属性指定了当前 Activity 可以自己处理的”配置信息“，然后调用onConfigurationChanged进行处理。

最常见的就是通过android:configChanges="orientation"告诉系统，当屏幕配置改变时，我们的Activity会自己处理，不需要再次onCreate。
参考：[onConfigurationChanged的作用](https://www.cnblogs.com/lijunamneg/archive/2013/03/26/2982461.html)

##### 2.3.1.2 屏蔽掉横竖屏的切换操作

在显示中我们可以屏蔽掉横竖屏的切换操作，这样就不会出现切换的过程中 Activity 生命周期重新加载的情况了，具体做法是，在 Activity 中加入如下语句：

```xml
android:screenOrientation="portrait" 始终以竖屏显示
android:screenOrientation="landscape" 始终以横屏显示
```

如果不想设置整个软件屏蔽横竖屏切换，只想设置屏蔽某个 Activity 的横竖屏切换功能的话，只需要下面操作：

```java
Activity.this.setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_PORTRAIT);以竖屏显示
Activity.this.setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE);以横屏显示
```

最后提一点,当你横竖屏切换的时候，如果走了销毁 Activity 的流程，那么需要保存当前和恢复当前 Activity 的状态的话，我们可以灵活运用 onSaveInstanceState()方法和onRestoreInstanceState()方法。

#### 2.3.2 什么时候 Activity 单独走 onPause()不走 onStop()？

启动 Dialog、启动透明 Activity。可以参考 2.3.4。

#### 2.3.3 什么时候导致 Activity 的 onDestory()不执行？

当用户后台强杀应用程序时，当前返回栈仅有一个 activity 实例时，这时候强杀，是会执行 onDestroy 方法的；当返回栈里面存在多个 Activity 实例时，栈里面的第一个没有销毁的 activity 会执行 onDestroy 方法，其他的不会执行；比如说：从 MainActivity 跳转到activity-A（或者继续从 activity-A 再跳转到 activity-B），这时候从后台强杀，只会执行 MainActivity 的 onDestroy 方法，activity-A（以及 activity-B）的 onDestroy 方法都不会执行；

#### 2.3.4 一个 A Activity 跳到一个 B Activity中，生命周期的走动，点击Back返回呢。如果一个 B Activity是透明的呢？如果 B Activity是一个Dialog呢？

```java
1）一个 A Activity 跳到一个 B Activity中，生命周期的走动
A：onPause
B：onCreate，onStart，onResume
A：onStop

2）点击Back返回
B：onPause
A：onRestart，onStart，onResume
B：onStop，onDestroy

3）如果B Activity是透明的
A：onPause
B：onCreate，onStart，onResume
A没有onStop了。

此时，返回键Back回到A：
B：onPause
A：onResume
B：onStop，onDestroy

4）如果 B Activity是一个Dialog
A：onPause()
B：onCreate()->onStart()->onResume()
A没有onStop了。

此时，返回键Back回到A：
B：onPause
A：onResume
B：onStop，onDestroy
```

注：如果是弹出一个非Activity的Dialog，是不会走onPause的。

小结：

1）中A被完全覆盖，会有onStop；

3）、4）中A被部分覆盖，不会有onStop。

### 2.4 其他回调方法

#### 2.4.1 onSaveInstanceState()和onRestoreInstanceState()

Activity 的 onSaveInstanceState() 和 onRestoreInstanceState()并不是生命周期方法，不同于 onCreate()、onPause()等生命周期方法，它们并不一定会被调用。当应用遇到意外情况（如：内存不足、用户直接按 Home 键）由系统销毁一个 Activity 时，onSaveInstanceState() 会被调用。但是当用户主动去销毁一个 Activity 时，例如在应用中按返回键，onSaveInstanceState()就不会被调用。 因为在这种情况下，用户的行为决定了不需要保存 Activity 的状态。通常 onSaveInstanceState() 只适合用于保存一些临时性的状态，而 onPause()适合用于数据的持久化保存。 在 activity 被杀掉之前调用保存每个实例的状态,以保证该状态可以在 onCreate(Bundle)或者 onRestoreInstanceState(Bundle) (传入的 Bundle参数是由onSaveInstanceState封装好的)中恢复。这个方法在一个activity 被杀死前调用，当该 activity 在将来某个时刻回来时可以恢复其先前状态。例如，如果 activity B 启用后位于 activity A 的前端，在某个时刻 activity A 因为系统回收资源的问题要被杀掉，A 通过 onSaveInstanceState 将有机会保存其用户界面状态，使得将来用户返回到 activity A 时能通过 onCreate(Bundle)或者 onRestoreInstanceState(Bundle)恢复界面的状态

例：在此方法中对用户写的内容进行保存

![image.png](https://upload-images.jianshu.io/upload_images/9000209-81b7c94dc1467d9f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

并在再次创建活动时恢复：

在onCreate中恢复：需要判断savedInstanceState是否为空。

![image.png](https://upload-images.jianshu.io/upload_images/9000209-b9cbcaee33ec2ae0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

或者在onRestoreInstanceState()中恢复，不需要判空。

![image.png](https://upload-images.jianshu.io/upload_images/9000209-b4ec4a3d879ced0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注：需要注意的是， onRestoreInstanceState()的回调是在onStart()之后的，所以如果保存的数据是希望用来恢复界面的，就不太适合放在这里取出了，而应该放到onCreate()中。

#### 2.4.2 onNewIntent()

![onNewIntent调用时机](https://upload-images.jianshu.io/upload_images/9000209-c48f3d59626c3315.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[Android——onNewIntent()的回调时机](https://blog.csdn.net/qq_36478274/article/details/105989145)

### 2.5 Activity 与进程的优先级

**前台>可见>服务>后台>空**

**前台：**与当前用户正在交互的 Activity 所在的进程。

**可见：**Activity 可见但是没有在前台所在的进程。

**服务：**Activity 在后台开启了 Service 服务所在的进程。

**后台：**Activity 完全处于后台所在的进程。

**空：**没有任何 Activity 存在的进程，优先级也是最低的。



原理

## 三、总结

本文总结：核心知识点

## 四、拓展

相关知识，横向、纵向对比；使用场景（示例）、适用范围、注意事项；

头脑风暴：联想相关知识，准备对比，为进一步深入学习做准备。

## 五、思维导图





**参考：**

1、