> 知识路径：Android > SharedPreferences
>
> version：2021/4/9
>
> review：2021/4/9
>
> 掌握程度：初学



前言：（可选）

## 一、预备知识

可选

## 二、SharedPreferences

### 2.1 概念

![image-20210409105027194](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210409105027194.png)

![image-20210409140228109](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210409140228109.png)

### 2.2 获取方式

#### 2.2.1 getPreferences

![image-20210409152248873](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210409152248873.png)

```java
    public SharedPreferences getPreferences(@Context.PreferencesMode int mode) {
        return getSharedPreferences(getLocalClassName(), mode);
    }
```

#### 2.2.2 getDefaultSharedPreferences

已弃用。

![image-20210409152555948](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210409152555948.png)

#### 2.2.3 getSharedPreferences

![image-20210409152725208](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210409152725208.png)

ContextImpl.java

```java
public SharedPreferences getSharedPreferences(String name, int mode) {
    // At least one application in the world actually passes in a null
    // name.  This happened to work because when we generated the file name
    // we would stringify it to "null.xml".  Nice.
    if (mPackageInfo.getApplicationInfo().targetSdkVersion <
            Build.VERSION_CODES.KITKAT) {
        if (name == null) {
            name = "null";
        }
    }

    File file;
    synchronized (ContextImpl.class) {
        if (mSharedPrefsPaths == null) {
            mSharedPrefsPaths = new ArrayMap<>();
        }
        file = mSharedPrefsPaths.get(name);
        if (file == null) {
            file = getSharedPreferencesPath(name);
            mSharedPrefsPaths.put(name, file);
        }
    }
    return getSharedPreferences(file, mode);
}
```

### 2.3 架构

![image-20210409153153669](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210409153153669.png)

![image-20210409153815122](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210409153815122.png)

### 2.4 apply/commit

![image-20210409154106051](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210409154106051.png)

### 2.5 注意

![image-20210409154424314](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210409154424314.png)





使用

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