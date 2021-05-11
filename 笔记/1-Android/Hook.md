> 知识路径：Android > 
>
> version：2021/4/12
>
> review：2021/4/12
>
> 掌握程度：初学



前言：（可选）

## 一、预备知识

可选

## 二、Hook

### 2.1 基本流程

![image-20210412102746560](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210412102746560.png)

### 2.2 使用示例

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Button btnHook = findViewById(R.id.btn_hook);
        btnHook.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Toast.makeText(MainActivity.this, "default click", Toast.LENGTH_LONG).show();
            }
        });
        hook(this, btnHook);
    }

    @SuppressLint({"DiscouragedPrivateApi", "PrivateApi"})
    public static void hook(Context context, View view) {
        try {
            // 反射执行 View 类的 getListenerInfo() 方法，拿到 v 的 mListenerInfo 对象，
            // 这个对象是点击事件的持有者
            Method method = View.class.getDeclaredMethod("getListenerInfo");
            // 由于 getListenerInfo() 方法并不是 public 的，所以要加这行代码来保证访问权限
            method.setAccessible(true);
            // 这里拿到的就是 mListenerInfo 对象，即点击事件的持有者
            Object mListenerInfo = method.invoke(view);

            // 要从这里面拿到当前的点击事件对象
            // 这是内部类的表示方法
            Class<?> listenerInfoClz = Class.forName("android.view.View$ListenerInfo");
            Field mOnClickListener = listenerInfoClz.getDeclaredField("mOnClickListener");
            // 取得真实的 mOnClickListener 对象
            final View.OnClickListener onClickListenerInstance = (View.OnClickListener) mOnClickListener.get(mListenerInfo);

            // 创建我们自己的点击事件代理类
            // 方式一 ：自己创建代理类
//            ProxyOnClickListener proxyOnClickListener = new ProxyOnClickListener(onClickListenerInstance);
//            mOnClickListener.set(mListenerInfo,proxyOnClickListener);

            // 方式二 ：由于 View.OnClickListener 是一个接口，所以可以直接用动态代理模式
            Object proxyOnClickListener = Proxy.newProxyInstance(context.getClass().getClassLoader(), new Class[]{View.OnClickListener.class},
                    new InvocationHandler() {
                        @Override
                        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                            // 加入自己的逻辑
                            Log.d("HookSetOnClickListener", "方式二：点击事件被hook到了");
                            // 执行被代理对象的逻辑
                            return method.invoke(onClickListenerInstance, args);
                        }
                    });
            mOnClickListener.set(mListenerInfo, proxyOnClickListener);
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (NoSuchFieldException e) {
            e.printStackTrace();
        }
    }

    // 自定义代理类
    static class ProxyOnClickListener implements View.OnClickListener {

        View.OnClickListener oriLis;

        public ProxyOnClickListener(View.OnClickListener oriLis) {
            this.oriLis = oriLis;
        }

        @Override
        public void onClick(View v) {
            // 加入自己的逻辑
            Log.d("HookSetOnClickListener", "方式一：点击事件被hook到了");
            // 执行被代理对象的逻辑
            if (oriLis != null) {
                oriLis.onClick(v);
            }
        }
    }
}
```



原理

## 三、总结

本文总结：核心知识点

## 四、思维导图

总结本文；把本文知识融入整体知识体系。

## 五、拓展

​	相关知识，横向、纵向对比；使用场景（示例）、适用范围、注意事项；

​	头脑风暴：联想相关知识，准备对比，为进一步深入学习做准备。



**参考：**

1、《Android核心知识点笔记V2020.03.30》