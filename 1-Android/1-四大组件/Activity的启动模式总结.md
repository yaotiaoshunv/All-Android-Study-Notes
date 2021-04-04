本文探讨了：
1、四种启动模式分别是什么？
2、它们的应用场景是什么？
3、onNewIntent的调用时机

在说启动模式之前，先掌握一个概念：返回栈。

### 一、返回栈

Android中的activity全都归属于task管理 。task 是多个 Activity 的集合， 这些 Activity 按照启动顺序排队存入一个栈（即“back stack”）。android默认会为每个App维持一个task来存放该app的所有activity，task的默认name为该app的packagename。

当然我们也可以在AndroidMainfest.xml中申明activity的taskAffinity属性来自定义task，但不建议使用，如果其他app也申明相同的task，它就有可能启动到你的activity，带来各种安全问题（比如拿到你的Intent）。

关于taskAffinity属性和Android的安全问题，后续在探讨。

返回栈和生命周期的联系总结一下：
1、启动新活动，入栈，位于栈顶。
2、按下back键或调用finish()，栈顶活动出栈。
3、系统总是显示位于栈顶的活动。






###二、启动模式
#####1、为什么需要启动模式？
先看一个例子：
若我们多次启动同一个Activity。系统会创建多个实例依次放入任务栈中。当按back键返回时，每按一次，一个Activity出栈，直到栈空为止。当栈中没有Activity时。系统就会回收此任务栈。
在这个例子中，同一个Activity被创建了多次，也就意味着：
1）内存存在多个相同Activity的实例，消耗了更多内存。
2）走了很多遍onCreate、onStart生命周期方法。

为此，Android为Activity 的创建提供了4种启动模式，而依据实际应用场景的不同。为Activity 选择不同的启动模式，最大化降低了每次都须要在栈中创建一个新的Activity的压力，降低内存使用。

#####2、启动模式介绍以及应用场景
![四种启动模式介绍](https://upload-images.jianshu.io/upload_images/9000209-81b1680415a656e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**1）standard （标准模式）**
描述：没有为Activity设置启动模式的话，默认为标准模式。每次启动一个Activity都会创建一个新的实例入栈，无论这个实例是否存在。
生命周期：每次被创建的实例Activity 的生命周期符合典型情况，它的onCreate、onStart、onResume都会被调用。
应用场景：默认都是。

**2）singleTop（栈顶复用模式）**
描述：如果当前启动的Activity在栈顶，则直接复用，否则创建一个新的实例。
生命周期：
如果栈顶的Activity被直接复用，由于它并没有发生改变，它的onCreate、onStart不会被系统调用，但是会回调一个新的方法 onNewIntent（Activity被正常创建时不会回调此方法）。
应用场景：
假设你在当前的Activity中又要启动同类型的Activity，此时建议将此类型Activity的启动模式指定为SingleTop
消息、通知页面

**3）singleTask（栈内复用模式）**
描述：如果要创建的Activity已经处于栈中时，则不会创建新的Activity，而是将存在栈中的Activity上面的其他Activity全部销毁，使它位于栈顶。
生命周期：如果在栈中，则直接复用，并回调onNewIntent方法。
应用场景：主activity一般用这个

**4）singleInstance（单实例模式）**
描述：singleInstance是全局单例模式，是一种加强的singleTask模式。它除了具有它所有特性外，还加强了一点：具有此模式的Activity只能单独位于一个任务栈中。
生命周期：如果栈存在，则复用。否在，新建一个栈，把Activity放入其中。
应用场景：经常用于系统中的应用，比如呼叫来电、闹钟、Launch、锁屏键的应用等等，整个系统中仅仅有一个。

**3、启动模式的使用方式**
1）在 Manifest.xml中指定Activity启动模式（静态）
```
<activity android:name="..activity.MultiportActivity" android:launchMode="singleTask"/>
```

2）启动Activity时。在Intent中指定启动模式去创建Activity（动态）
在 new 一个 Intent 后，通过Intent的addFlags方法去动态指定一个启动模式。
```
Intent intent = new Intent();
intent.setClass(context, MainActivity.class);
intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);        
context.startActivity(intent);
```

二者的区别：
1）优先级：动态优先。若两者都存在，以addFlags为准。
2）限定范围：静态无法为Activity直接指定 FLAG_ACTIVITY_CLEAR_TOP 标识；动态无法为Activity指定 singleInstance 模式。


**4、Activity 的 Flags**（切勿死记，理解它）
1） FLAG_ACTIVITY_NEW_TASK
作用是为Activity指定 “SingleTask”启动模式。跟在AndroidMainfest.xml指定效果同样。
2） FLAG_ACTIVITY_SINGLE_TOP
作用是为Activity指定 “SingleTop”启动模式，跟在AndroidMainfest.xml指定效果同样。
3）FLAG_ACTIVITY_CLEAN_TOP
4）FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS
具有此标记位的Activity不会出历史Activity的列表中。它等同于在xml中指定Activity的属性：android:excludeFromRecents="ture"





###三、对SingleTop/SingleTask与onNewIntent的分析
我们需要考虑一个Activity跳转时携带页面參数的问题。如下：

由于当一个Activity设置了SingleTop或者SingleTask模式后，跳转此Activity出现复用原有Activity的情况时，此Activity的onCreate方法将不会再次运行。onCreate方法仅仅会在第一次创建Activity时被运行。而一般onCreate方法中会进行该页面的数据初始化、UI初始化，假设页面的展示数据与页面跳转传递的參数无关，则不必操心此问题；若页面展示的数据就是通过getInten() 方法来获取的，那么问题就会出现：getInten()获取的一直都是老数据，根本无法接收跳转时传送的新数据！

示例：
```
<activity
            android:name=".activity.CourseDetailActivity"
            android:launchMode="singleTop"
            android:screenOrientation="portrait" />
```
```
public class CourseDetailActivity extends BaseActivity{
  ......
  @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_course_detail_layout);
        initData();
        initView();
    }
 
   //初始化数据
    private void initData() {
        Intent intent = getIntent();
        mCourseID = intent.getStringExtra(COURSE_ID);
    }
 
    //初始化UI
    private void initView() {
    ......
    }
    ......
}
```
以上代码中的CourseDetailActivity在配置文件里设置了启动模式是SingleTop模式，依据上面启动模式的介绍可得知，当CourseDetailActivity处于栈顶时。再次跳转页面到CourseDetailActivity时会直接复用原有的Activity，并且此页面须要展示的数据是从getIntent(）方法得来，可是initData()方法不会再次被调用，此时页面就无法显示新的数据。

这时我们需要另外一个回调 onNewIntent（Intent intent）方法。此方法会传入最新的intent，这样我们就能够解决上述问题。这里建议的方法是又一次去setIntent。然后又一次去初始化数据和UI。代码例如以下所看到的：
```
    /*
    * 复用Activity时的生命周期回调
    */
    @Override
    protected void onNewIntent(Intent intent) {
        super.onNewIntent(intent);
        setIntent(intent);
        initData();
        initView();
    }
```
这样，在一个页面中能够反复跳转并显示不同的内容。
注：当调用到onNewIntent(intent)的时候，需要在onNewIntent() 中使用setIntent(intent)赋值给Activity的Intent。否则，后续的getIntent()都是得到老的Intent。

那么onNewIntent都会在什么情况下调用呢？
1）当ActivityA的LaunchMode为SingleTop时：如果ActivityA在栈顶,且现在要再启动ActivityA，这时会调用onNewIntent()方法 
2）当ActivityA的LaunchMode为SingleInstance,SingleTask时：如果已经ActivityA已经在堆栈中，那么此时会调用onNewIntent()方法 
3）当ActivityA的LaunchMode为Standard时：由于每次启动ActivityA都是启动新的实例，和原来启动的没关系，所以不会调用原来ActivityA的onNewIntent方法

我的理解是：只要启动的Activity被复用了，就会调用onNewIntent。因为经过我的测试，即使是standard，如果home后，点击启动按钮，还是会调用onNewIntent的。





###四、Activity的状态与回收
知道了生命周期和返回栈后，就可以来探讨下活动的状态和回收了。

每个Activity在其生命周期中最多可能有四种状态。
| 活动状态    | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| 1、运行状态 | 位于栈顶。可交互。系统最不愿回收。                           |
| 2、暂停状态 | 不在栈顶，仍然可见。<br/>情形一：跳转当活动跳转时，第一个活动会先调用onPause()，然后等待第二个活动显示onCreate() –》onStart()，在活动二从创建到显示的过程中，活动一处于暂停状态且可见。<br/><br/>注：onPause()执行完成后，第二个活动才会onCreate(),所以onPause中执行的操作，要尽量快速，或者放到子线程中，以避免对UI线程的影响。<br/><br/>情形二：启动一个非全屏的活动。<br/>比如启动了一个对话框形式的活动。暂停状态的活动还完全存活着，系统也不愿回收(它还可见，在内存极低的情况下，系统才会考虑回收)。 |
| 3、停止状态 | 不在栈顶，完全不可见。系统会保存相应的状态和成员变量，但不可靠。因为当其他地方需要内存时，有可能被回收。 |
| 4、销毁状态 | 移出返回栈。最倾向于回收。                                   |

注：回收，就是系统把一个对象从内存中去除，以保证内存充足。因为一个活动即使被finish()，回调了onDestory()，也只是把活动移出管理栈，并没有置为null，还存在于内存中。





参考：
1、参考和借鉴了此文，尤其是对onNewIntent的分析，对我很有帮助，感谢：[https://blog.csdn.net/elisonx/article/details/80397519](https://blog.csdn.net/elisonx/article/details/80397519)





写于：2020/09/08