> 知识路径：Android > Java > 关键字
>
> version：2021/4/1
>
> review：2021/4/1

![image-20210401174727150](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210401174727150.png)

![image-20210401174736826](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210401174736826.png)

（1）被final关键字修饰的基本数据类型，则其数值一旦在初始化之后便不能更改;
（2）如果是引用类型的变量，则在对其初始化之后便不能再让其指向另一个对象,但该引用所指向的对象的内容是可以发生变化的。原因为：引用数据类型存储的引用对象在堆内存中的地址，final修饰引用类型之后，要求该引用指向的堆内存空间（或者说该引用存储的堆内存地址）不能改变。

当用final修饰类的静态成员变量时，静态成员变量的初始化方式也有两种：
（5）在声明时进行初始化
（6）在静态初始化块中进行初始化
当用final修饰接口的静态变量时，其初始化方式只有一种：

（7）在声明时进行初始化



**final修饰参数**

修饰基本数据类型：在方法内，不能修改其值。

![image-20210401180212393](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210401180212393.png)

修饰引用类型：该引用不能指向其他对象或赋值为null，但可以修改引用对象的内容。

![image-20210401180455622](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210401180455622.png)

final用于修饰参数的目的并非防止在调用的方法内部对参数的操作改变方法外部对应变量的值，只是防止在该方法内对该参数进行重新赋值操作，影响该参数传递时的初始值。