> 知识路径：Android > 
>
> version：2021/4/
>
> review：2021/4/
>
> 掌握程度：初学



前言：（可选）

### 一、预备知识

可选

### 二、Parcelable

![image-20210408154625727](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408154625727.png)

2.2 使用示例

```java
public class User implements Parcelable {
    private int userId;

    protected User(Parcel in) {
        userId = in.readInt();
    }

    public static final Creator<User> CREATOR = new Creator<User>() {
        @Override
        public User createFromParcel(Parcel in) {
            return new User(in);
        }

        @Override
        public User[] newArray(int size) {
            return new User[size];
        }
    };

    @Override
    public int describeContents() {
        return 0;
    }

    @Override
    public void writeToParcel(Parcel dest, int flags) {
        dest.writeInt(userId);
    }

    public int getUserId() {
        return userId;
    }
}
```



2.3 方法说明

![image-20210408155127170](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408155127170.png)

![image-20210408155222193](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408155222193.png)

![image-20210408155232843](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408155232843.png)



2.4 Parcelable 与 Serializable 对比

![image-20210408154731673](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408154731673.png)



概念

使用

原理

### 三、总结

Parcel 是包裹的意思。可以这么去理解 Parcelable ：序列化就是将 bean（Parcelable的实现类）中的数据放入 Parcel 对象中，反序列化就是将数据从 Parcel 中取出来，并重建(new) bean。

往 Parcel 中加入数据通过 writeToParcel()方法；

往 Parcel 中取数据通过 Creator.createFromParcel()、Creator.newArray()。

包裹（Parcel）的传输方式有：Intent、Binder。

### 四、思维导图

总结本文；把本文知识融入整体知识体系。

### 五、拓展

​	相关知识，横向、纵向对比；使用场景（示例）、适用范围、注意事项；

​	头脑风暴：联想相关知识，准备对比，为进一步深入学习做准备。



**参考：**

1、《Android核心知识点笔记V2020.03.30》