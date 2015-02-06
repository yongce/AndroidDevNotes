# Service Notes

## Service Binding

### 关于Serivce#onBind()

当client第一次调用bindService()时，系统会回调该Service#onBind()方法，并把返回的IBinder对象缓存起来。此时，若有其它client也调用bindService()，那么系统会直接把缓存的IBinder对象传递给client。**也就是说，在Service#onCreate()和Service#onDestroy()期间，Service#onBind()方法会且仅会被调用一次，而不管有多少client来bind它，也不管Service#onUnbind()是否被回调了。**

当所有client都调用了unbindService()，Service#onUnbind()会被回调。如果此时Service#onUnbind()返回true，且Service#onDestroy()没有被调用（说明此Service被startService()过了），则下次client来bind Service时，Service#onRebind()会被回调。

Service#onBind()和Service#onUnbind()，或者Service#onRebind()和Service#onUnbind()，是成对出现的，一一对应关系。

### IBinder的三种实现方式

#### 扩展Binder类

如果Service和client在同一进程中，那么可以选择直接扩展Binder类，并添加相应的公开方法，而在client的ServiceConnection#onServiceConnected()中，直接强转IBinder对象为所定义的Binder类对象。

Service示例代码：
```java
    private final IBinder mBinder = new LocalBinder();

    public class LocalBinder extends Binder {
        // Add the APIs
    }

    @Override
    public IBinder onBind(Intent intent) {
        return mBinder;
    }
```

client示例代码：
```java
    private ServiceConnection mConnection = new ServiceConnection() {
        @Override
        public void onServiceConnected(ComponentName className,
                IBinder service) {
            LocalBinder binder = (LocalBinder) service;
        }
    };
```

#### Messenger

如果Service和client在不同进程中，但仅需要相互传送一些数据，且这些数据可以通过Message传送，那么可以选择使用Messenger。**由于Message最终是在Handler中处理的，因此所有client的请求无法并行执行。**

#### 使用AIDL

如果Service和client在不同进程中，可以选用AIDL，这样Service可以暴露一些接口给client调用。由于这些AIDL接口可以同时被多个client调用，因此需要实现为线程安全的。

如果client和Service在不同进程中，那么client拿到的IBinder对象是一个android.os.BinderProxy代理对象；如果client和Service在同一进程中，那么client拿到的IBinder对象就是Service#onBind()返回的IBinder对象。（参见后面的示例代码）

当Service进程中IBinder实现代码发生的异常时：a) 如果client和Service在不同进程中，那么此crash会被系统处理掉，不会传递到client（client和Service都感知不到此crash，在系统Log能够看到相应信息）；b) 如果client和Service在同一进程中，那么此crash会直接传递到client（因为这里是client直接调用了Service中IBinder方法！）。（参见后面的示例代码）

默认情况下，调用远程IBinder方法时，client能够捕获到的唯一异常是DeadObjectException（一个特殊的RemoteException），表示这个IBinder连接挂了。如果要IBinder要向client传递异常，则需要通过android.os.Parcel#writeException()和android.os.Parcel#readException()来实现。（参见后面的示例代码）

## 示例代码

提供了一个Demo应用，演示了IBinder的手动实现和上面提到的一些技术点。

代码地址：https://github.com/dxopt/BinderDemo

## 参考资料
1. http://developer.android.com/guide/components/services.html
2. http://developer.android.com/guide/components/bound-services.html
3. http://developer.android.com/guide/components/aidl.html
