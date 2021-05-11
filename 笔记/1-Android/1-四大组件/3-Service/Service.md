> 知识路径：Android > 四大组件 >Service
>
> version：2021/4/8
>
> review：2021/4/8
>
> 掌握程度：了解



前言：（可选）

### 一、预备知识

Handler、Service使用

### 二、Service

2.1 启动过程

![image-20210408141015701](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408141015701.png)

ActivityThread.java

```java
    private void handleCreateService(CreateServiceData data) {
        // If we are getting ready to gc after going to the background, well
        // we are back active so skip it.
        unscheduleGcIdler();

        LoadedApk packageInfo = getPackageInfoNoCheck(
                data.info.applicationInfo, data.compatInfo);
        Service service = null;
        try {
            if (localLOGV) Slog.v(TAG, "Creating service " + data.info.name);

            ContextImpl context = ContextImpl.createAppContext(this, packageInfo);
            Application app = packageInfo.makeApplication(false, mInstrumentation);
            java.lang.ClassLoader cl = packageInfo.getClassLoader();
            service = packageInfo.getAppFactory()
                    .instantiateService(cl, data.info.name, data.intent);
            // Service resources must be initialized with the same loaders as the application
            // context.
            context.getResources().addLoaders(
                    app.getResources().getLoaders().toArray(new ResourcesLoader[0]));

            context.setOuterContext(service);
            service.attach(context, this, data.info.name, data.token, app,
                    ActivityManager.getService());
            service.onCreate();
            mServices.put(data.token, service);
            try {
                ActivityManager.getService().serviceDoneExecuting(
                        data.token, SERVICE_DONE_EXECUTING_ANON, 0, 0);
            } catch (RemoteException e) {
                throw e.rethrowFromSystemServer();
            }
        } catch (Exception e) {
            if (!mInstrumentation.onException(service, e)) {
                throw new RuntimeException(
                    "Unable to create service " + data.info.name
                    + ": " + e.toString(), e);
            }
        }
    }
```



2.2 绑定过程

![image-20210408141628454](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408141628454.png)

ActivityThread.java

```java
    private void handleBindService(BindServiceData data) {
        Service s = mServices.get(data.token);
        if (DEBUG_SERVICE)
            Slog.v(TAG, "handleBindService s=" + s + " rebind=" + data.rebind);
        if (s != null) {
            try {
                data.intent.setExtrasClassLoader(s.getClassLoader());
                data.intent.prepareToEnterProcess();
                try {
                    if (!data.rebind) {
                        IBinder binder = s.onBind(data.intent);
                        ActivityManager.getService().publishService(
                                data.token, data.intent, binder);
                    } else {
                        s.onRebind(data.intent);
                        ActivityManager.getService().serviceDoneExecuting(
                                data.token, SERVICE_DONE_EXECUTING_ANON, 0, 0);
                    }
                } catch (RemoteException ex) {
                    throw ex.rethrowFromSystemServer();
                }
            } catch (Exception e) {
                if (!mInstrumentation.onException(s, e)) {
                    throw new RuntimeException(
                            "Unable to bind to service " + s
                            + " with " + data.intent + ": " + e.toString(), e);
                }
            }
        }
    }
```



2.3 生命周期

![image-20210408142044307](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408142044307.png)

![image-20210408142656818](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408142656818.png)

 ![image-20210408142709474](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408142709474.png)



2.4 启动前台服务

![image-20210408142847720](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408142847720.png)







概念

使用

原理

### 三、总结

本文总结：核心知识点

### 四、思维导图

总结本文；把本文知识融入整体知识体系。

### 五、拓展

​	相关知识，横向、纵向对比；使用场景（示例）、适用范围、注意事项；

​	头脑风暴：联想相关知识，准备对比，为进一步深入学习做准备。



**参考：**

1、《Android核心知识点笔记V2020.03.30》