> 知识路径：Android > Java > 关键字
>
> version：2021/3/31
>
> review：null



- static 关键字修饰的方法或者变量可以直接通过类名访问。
- 不能直接使用对象实例访问静态方法和静态变量。见例1。
- 静态变量被所有的对象所共享，在内存中仅有一个副本，当且仅当类初次加载时会被初始化。
- 能通过this访问静态变量吗？所有的静态方法和静态变量都可以通过对象访问。（只要权限足够）
- static不能用来修饰局部变量。





例1：

```java
public class A {
    public static int x = 0;

    public void m2() {
        x = 3;
        m1();
    }

    public static void m1() {
        System.out.println("sss");
    }
}
```

![image-20210331174746801](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210331174746801.png)

不能通过对象直接访问（调用） x 和 m1 。