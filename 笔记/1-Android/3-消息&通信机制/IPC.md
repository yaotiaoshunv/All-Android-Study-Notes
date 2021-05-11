> 
>
> 知识路径：Android > IPC
>
> version：2021/4/8
>
> review：2021/4/8
>
> 掌握程度：初学



前言：（可选）

### 一、预备知识

可选

### 二、IPC

2.1 概念

![image-20210408160250512](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408160250512.png)

2.2 IPC方式

![image-20210408160450941](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408160450941.png)



2.3 Binder

![image-20210408160742163](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408160742163.png)

![image-20210408160828890](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408160828890.png)

2.4 AIDL

![image-20210408161212939](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408161212939.png)



![image-20210408161812402](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408161812402.png)

![image-20210408161204288](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408161204288.png)

系统会自动生成：

```java
/*
 * This file is auto-generated.  DO NOT MODIFY.
 */
package com.lizw.staticanddynamicproxydemo;
// Declare any non-default types here with import statements

public interface IRemoteService extends android.os.IInterface
{
  /** Default implementation for IRemoteService. */
  public static class Default implements com.lizw.staticanddynamicproxydemo.IRemoteService
  {
    /**
         * Demonstrates some basic types that you can use as parameters
         * and return values in AIDL.
         */
    @Override public int getUserId() throws android.os.RemoteException
    {
      return 0;
    }
    @Override
    public android.os.IBinder asBinder() {
      return null;
    }
  }
  /** Local-side IPC implementation stub class. */
  public static abstract class Stub extends android.os.Binder implements com.lizw.staticanddynamicproxydemo.IRemoteService
  {
    private static final java.lang.String DESCRIPTOR = "com.lizw.staticanddynamicproxydemo.IRemoteService";
    /** Construct the stub at attach it to the interface. */
    public Stub()
    {
      this.attachInterface(this, DESCRIPTOR);
    }
    /**
     * Cast an IBinder object into an com.lizw.staticanddynamicproxydemo.IRemoteService interface,
     * generating a proxy if needed.
     */
    public static com.lizw.staticanddynamicproxydemo.IRemoteService asInterface(android.os.IBinder obj)
    {
      if ((obj==null)) {
        return null;
      }
      android.os.IInterface iin = obj.queryLocalInterface(DESCRIPTOR);
      if (((iin!=null)&&(iin instanceof com.lizw.staticanddynamicproxydemo.IRemoteService))) {
        return ((com.lizw.staticanddynamicproxydemo.IRemoteService)iin);
      }
      return new com.lizw.staticanddynamicproxydemo.IRemoteService.Stub.Proxy(obj);
    }
    @Override public android.os.IBinder asBinder()
    {
      return this;
    }
    @Override public boolean onTransact(int code, android.os.Parcel data, android.os.Parcel reply, int flags) throws android.os.RemoteException
    {
      java.lang.String descriptor = DESCRIPTOR;
      switch (code)
      {
        case INTERFACE_TRANSACTION:
        {
          reply.writeString(descriptor);
          return true;
        }
        case TRANSACTION_getUserId:
        {
          data.enforceInterface(descriptor);
          int _result = this.getUserId();
          reply.writeNoException();
          reply.writeInt(_result);
          return true;
        }
        default:
        {
          return super.onTransact(code, data, reply, flags);
        }
      }
    }
    private static class Proxy implements com.lizw.staticanddynamicproxydemo.IRemoteService
    {
      private android.os.IBinder mRemote;
      Proxy(android.os.IBinder remote)
      {
        mRemote = remote;
      }
      @Override public android.os.IBinder asBinder()
      {
        return mRemote;
      }
      public java.lang.String getInterfaceDescriptor()
      {
        return DESCRIPTOR;
      }
      /**
           * Demonstrates some basic types that you can use as parameters
           * and return values in AIDL.
           */
      @Override public int getUserId() throws android.os.RemoteException
      {
        android.os.Parcel _data = android.os.Parcel.obtain();
        android.os.Parcel _reply = android.os.Parcel.obtain();
        int _result;
        try {
          _data.writeInterfaceToken(DESCRIPTOR);
          boolean _status = mRemote.transact(Stub.TRANSACTION_getUserId, _data, _reply, 0);
          if (!_status && getDefaultImpl() != null) {
            return getDefaultImpl().getUserId();
          }
          _reply.readException();
          _result = _reply.readInt();
        }
        finally {
          _reply.recycle();
          _data.recycle();
        }
        return _result;
      }
      public static com.lizw.staticanddynamicproxydemo.IRemoteService sDefaultImpl;
    }
    static final int TRANSACTION_getUserId = (android.os.IBinder.FIRST_CALL_TRANSACTION + 0);
    public static boolean setDefaultImpl(com.lizw.staticanddynamicproxydemo.IRemoteService impl) {
      // Only one user of this interface can use this function
      // at a time. This is a heuristic to detect if two different
      // users in the same process use this function.
      if (Stub.Proxy.sDefaultImpl != null) {
        throw new IllegalStateException("setDefaultImpl() called twice");
      }
      if (impl != null) {
        Stub.Proxy.sDefaultImpl = impl;
        return true;
      }
      return false;
    }
    public static com.lizw.staticanddynamicproxydemo.IRemoteService getDefaultImpl() {
      return Stub.Proxy.sDefaultImpl;
    }
  }
  /**
       * Demonstrates some basic types that you can use as parameters
       * and return values in AIDL.
       */
  public int getUserId() throws android.os.RemoteException;
}

```

![image-20210408161851035](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408161851035.png)

![image-20210408161858714](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408161858714.png)



![image-20210408162105673](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408162105673.png)

![image-20210408162117662](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408162117662.png)

![image-20210408162127889](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408162127889.png)

![image-20210408162133433](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408162133433.png)

![image-20210408162143468](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408162143468.png)

![image-20210408162153786](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408162153786.png)

**到时候把 AIDL 的示例移出去另外做一篇笔记。**



2.5 Messenger

![image-20210408162311106](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210408162311106.png)



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