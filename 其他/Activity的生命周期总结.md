关于Activity的生命周期，主要得掌握：
1、在不同情况下，生命周期的变化过程。主要有：
1）横竖屏切换
2）A-->B，B是透明的/dialog。
2、onConfigurationChanged、onSaveInstanceState、onNewIntent的使用场景。
以后复习重点看就第二/第三节，把其中提出的问题都掌握。

#### 一、 生命周期
#####1.1 生命周期图片
![Activity生命周期官方图片](https://upload-images.jianshu.io/upload_images/9000209-289f2eb9ab4e9664.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

| 回调方法    | 方法说明                         |
| ----------- | -------------------------------- |
| onCreate()  | 不可见。加载视图，绑定事件。     |
| onStart()   | 不可见   &rarr;   可见。         |
| onResume()  | 可交互。栈顶，运行状态。         |
| onPause()   | 可见。保存数据。释放资源。       |
| onStop()    | 不可见。                         |
| onDestory() | 销毁，出栈。                     |
| onRestart() | 停止   &rarr;   运行，之前回调。 |

##### 1.2 回调方法与生存期的对应关系
除了onRestart()，生命周期两两相对，可分为3种生存期。
* 完整生存期
onCreate() &rarr; onDestory()。
* 可见生存期
onStart &rarr; onStop()。
* 前台生存期
onResume() &rarr; onPause()。





#### 二、 几种常见的回调总结
| 常见操作 | 回调步骤                                                     |
| :------: | ------------------------------------------------------------ |
|   跳转   | FirstActivity：onPause()    <br/>   SecondActivity：        &rarr; onCreate() &rarr; onStart() &rarr; onResume    <br/>    FirstActivity:    &rarr; onStop() |
|  返回键  | OnPause() &rarr; onStop() &rarr; onDestory()                 |
|  Home键  | onPause &rarr; onSaveInstanceState() &rarr; onStop()         |
|  再启动  | onRestart() &rarr; onStart() &rarr; onResume                 |
|  横竖屏  | 销毁重建。即onPause &rarr; onDestory &rarr; onCreate &rarr; onResume。<br/>网上有说横 &rarr; 竖：会执行两次。                                                                  <br/>我用vivoX6Plus-Android5.1.1测试，只会执行一次流程。                                 <br/>不同手机、Android版本应该有不同的回调方式。 |
|   锁屏   | onPause &rarr; onSaveInstanceState &rarr; onStop()           |
|  再启动  | onRestart() &rarr; onStart() &rarr; onResume                 |

补充
当前Activity产生事件弹出Toast和AlertDialog的时候Activity的生命周期不会有改变
Activity运行时按下HOME键(跟被完全覆盖是一样的)：onSaveInstanceState -->
onPause --> onStop onRestart -->onStart--->onResume
Activity未被完全覆盖只是失去焦点：onPause--->onResume

面试相关问题：
**1、屏幕横竖屏切换时的生命周期变化**
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

4）修改AndroidManifest.xml，给该Activity添加
**android:configChanges="orientation"**
然后**切换为横屏**
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

总结：
1、不设置Activity的android:configChanges时，切屏会重新调用各个生命周期，
切横屏时会执行一次，切竖屏时会执行两次
2、设置Activity的android:configChanges="orientation"时，切屏还是会重新调
用各个生命周期，切横、竖屏时只会执行一次
3、设置Activity的android:configChanges="orientation|keyboardHidden"时，
切屏不会重新调用各个生命周期，只会执行onConfigurationChanged方法

**2、一个 A Activity 跳到一个 B Activity中，生命周期的走动，点击Back返回呢。如果一个 A Activity是透明的呢？如果 B Activity是一个Dialog呢？横竖屏切换生命周期走动，以及是否了解onConfigurationChanged。**
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

注：如果是弹出一个非Activity的Dialog，是不会走onPause的。

小结：1）中A被完全覆盖，会有onStop；3）、4）中A被部分覆盖，不会有onStop。

5）横竖屏切换生命周期走动
见问题1。

6）是否了解onConfigurationChanged？
当Configuration改变后，ActivityManagerService将会发送"配置改变"的广播，会要求ActivityThread 重新启动当前focus的Activity，这是默认情况，我们不做任何处理。如果我们用android:configChanges来配置Activity信息，那么就可以避免对Activity销毁再重新创建，而是调用onConfigurationChanged方法。

onConfigurationChanged方法一般与android:configChanges属性成双成对，android:configChanges属性指定了当前Activity可以自己处理的”配置信息“，然后调用onConfigurationChanged进行处理。

最常见的就是通过android:configChanges="orientation"告诉系统，当屏幕配置改变时，我们的Activity会自己处理，不需要再次onCreate。
参考：[https://www.cnblogs.com/lijunamneg/archive/2013/03/26/2982461.html](https://www.cnblogs.com/lijunamneg/archive/2013/03/26/2982461.html)





#### 三、 其他回调方法
#####1、onSaveInstanceState()和onRestoreInstanceState()
Activity 的 onSaveInstanceState() 和 onRestoreInstanceState()并不是生命周期方法，不同于 onCreate()、onPause()等生命周期方法，它们并不一定会被调用。当应用遇到意外情况（如：内存不足、用户直接按 Home 键）由系统销毁一个 Activity 时，onSaveInstanceState() 会被调用。但是当用户主动去销毁一个 Activity 时，例如在应用中按返回键，onSaveInstanceState()就不会被调用。 因为在这种情况下，用户的行为决定了不需要保存 Activity 的状态。通常 onSaveInstanceState() 只适合用于保存一些临时性的状态，而 onPause()适合用于数据的持久化保存。 在 activity 被杀掉之前调用保存每个实例的状态,以保证该状态可以在 onCreate(Bundle)或者 onRestoreInstanceState(Bundle) (传入的 Bundle参数是由onSaveInstanceState封装好的)中恢复。这个方法在一个activity 被杀死前调用，当该 activity 在将来某个时刻回来时可以恢复其先前状态。例如，如果 activity B 启用后位于 activity A 的前端，在某个时刻 activity A 因为系统回收资源的问题要被杀掉，A 通过 onSaveInstanceState 将有机会保存其用户界面状态，使得将来用户返回到 activity A 时能通过 onCreate(Bundle)或者 onRestoreInstanceState(Bundle)恢复界面的状态

例：在此方法中对用户写的内容进行保存

![image.png](https://upload-images.jianshu.io/upload_images/9000209-81b7c94dc1467d9f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

并在再次创建活动时恢复：

在onCreate中恢复：需要判断savedInstanceState是否为空。

![image.png](https://upload-images.jianshu.io/upload_images/9000209-b9cbcaee33ec2ae0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

或者在onRestoreInstanceState()中恢复，不需要判空。

![image.png](https://upload-images.jianshu.io/upload_images/9000209-b4ec4a3d879ced0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注：需要注意的是， onRestoreInstanceState()的回调是在onStart()之后的，所以如果保存的数据是希望用来恢复界面的，就不太适合放在这里取出了，而应该放到onCreate()中。

#####2、onNewIntent()。
![onNewIntent调用时机](https://upload-images.jianshu.io/upload_images/9000209-c48f3d59626c3315.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[https://blog.csdn.net/qq_36478274/article/details/105989145](https://blog.csdn.net/qq_36478274/article/details/105989145)





####四、其他
我对可见、可交互的理解：

1、当跳转的时候，可见只是意为着当前活动可见，并不是说，下一个活动在onStart执行完就可见，还是要等到onResume执行结束才能看见。
2、可交互，是指在执行onPause &rarr; onResume之间，不可交互，要到onResume结束后才可以交互。

写于：2020/09/07