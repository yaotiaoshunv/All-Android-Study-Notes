> 知识路径：Android > 四大组件 > ContentProvider
>
> version：2021/4/8
>
> review：2021/4/8
>
> 掌握程度：初学



前言：（可选）

## 一、预备知识

可选

## 二、ContentProvider

### 2.1 概念

内容提供器主要用于跨程序共享数据，同时保证被访问数据的安全性。

不同于文件存储和SharedPreferences存储中的两种全局可读写操作模式（4.2开始废弃了），ContentProvider可以选择只对哪一部分的数据进行共享，从而保证程序中的隐私数据不会泄露。

![image-20210408143622799](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408143622799.png)

### 2.2 基本使用：访问其他程序中的数据

要访问内容提供器中共享的数据，需要使用ContentResolver类，它提供了一系列方法用于对数据增删改查。

#### 2.2.1 query()

参数说明：

![img](file:///C:\Users\NJCS\AppData\Local\Temp\ksohtml4488\wps1.jpg) 

![img](file:///C:\Users\NJCS\AppData\Local\Temp\ksohtml4488\wps2.jpg) 

查询后返回一个Cursor对象。

![img](file:///C:\Users\NJCS\AppData\Local\Temp\ksohtml4488\wps3.jpg) 

#### 2.2.2 insert()

![img](file:///C:\Users\NJCS\AppData\Local\Temp\ksohtml4488\wps4.jpg)

#### 2.2.3 update()

![img](file:///C:\Users\NJCS\AppData\Local\Temp\ksohtml4488\wps5.jpg) 

#### 2.2.4 delete()

![img](file:///C:\Users\NJCS\AppData\Local\Temp\ksohtml4488\wps6.jpg) 

### 2.3 访问示例

2.3.1 读取联系人

![img](file:///C:\Users\NJCS\AppData\Local\Temp\ksohtml4488\wps7.jpg) 

2.3.2 读取sd卡中的视频文件

![img](file:///C:\Users\NJCS\AppData\Local\Temp\ksohtml4488\wps8.jpg) 

小结：使用ContentResolver查询数据时，有两个核心参数：uri，projection。

在获取系统的数据时，Uri和projection(列名)通过对应的类获取，比如联系人信息，由ContactsContract提供；视频文件信息由MediaStore.Video提供。



## 三、创建内容提供者

要创建内容提供者，需要继承ContentProvider，并实现其中的方法。

#### 1、 onCreate()

初始化内容提供器的时候调用。当有ContentResolver访问程序数据时，内容提供器才会被初始化。通常在此完成数据库的创建和升级，返回值表示是否成功。

增删改查，使用的都是SQLiteDatebase提供的方法。需要注意的是，不同的uri有一点区别：

**URI的两种主要写法：**

​	调用方期望访问com.example.app中的table表。

​		content://com.example.app.provider/table

​	调用方期望访问com.example.app中的table表中id为1的数据。

​		content://com.example.app.provider/table/1

可以通过通配符的方式来匹配这两种格式的URI。

​	*：匹配任意长度的任意字符。

​	#：匹配任意长度的数字。

匹配任意表的URI的格式：

​	content://com.example.app.provider/*

匹配table表中的任意一行的URI格式：

​	Content://com.example.app.provider/table/#

因此，在使用增删改查的四个方法时，需要先判断一下uir的类型，对于查看全表的，用标准方法即可。对于带id的，需要先获取id，在根据id去操作。

​	获取id：uro.getPathSegments().get(1);

​	使用此方法可以获取到uri最后的id。

四种操作具体如下：（执行完相应操作后，需要给出返回值）

1、 insert()

![img](file:///C:\Users\NJCS\AppData\Local\Temp\ksohtml4488\wps9.jpg) 

2、 delete()

![img](file:///C:\Users\NJCS\AppData\Local\Temp\ksohtml4488\wps10.jpg) 

3、 update()

![img](file:///C:\Users\NJCS\AppData\Local\Temp\ksohtml4488\wps11.jpg) 

4、 query()

![img](file:///C:\Users\NJCS\AppData\Local\Temp\ksohtml4488\wps12.jpg) 

5、 getType()

这个方法要返回一个uri的MIME类型。是一个字符串，格式如下。

![img](file:///C:\Users\NJCS\AppData\Local\Temp\ksohtml4488\wps13.jpg)





使用

原理

### 三、总结

ContentProvider作为跨程序共享数据的途径之一，在获取数据的程序中，核心在于ContentResolver类，使用这个类可以获取其他程序中的数据。

对于提供数据的程序，需要在ContentProvider提供的方法中重写具体的操作逻辑，这也是实现控制数据安全的关键。每个方法都有对应的返回值，这些返回值将作为结果提供给请求方。

ContentResolver、ContentProvider、数据库或者文件之间的关系：

ContentProvider算是一个守卫者，它看管着数据，外界（其他程序）想要获取数据（实施者是ContentResolver），必须经过它的同意（判断uri），并根据请求，执行相应操作，最终返回数据。这样就可以在保证数据安全的情况下，对外提供数据。

### 四、思维导图

总结本文；把本文知识融入整体知识体系。

### 五、拓展

​	相关知识，横向、纵向对比；使用场景（示例）、适用范围、注意事项；

​	头脑风暴：联想相关知识，准备对比，为进一步深入学习做准备。



**参考：**

1、《Android核心知识点笔记V2020.03.30》