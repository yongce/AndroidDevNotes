# GC Watcher

反馈与建议，请移步：
https://github.com/yongce/AndroidDevNotes/issues/3

在看Android Framework源码时发现的一小段有意思的代码，涉及到几个有意思的知识点。

代码整理如下（来自类com.android.internal.os.BinderInternal）：
```java
public class GcWatcher {
    static WeakReference<GcWatcher> mGcWatcher
            = new WeakReference<GcWatcher>(new GcWatcher());
    static long mLastGcTime;

    private GcWatcher() {
        // nothing to do
    }

    public static void setupWatcher() {
        // nothing to do
    }

    @Override
    protected void finalize() throws Throwable {
        super.finalize();
        mLastGcTime = System.currentTimeMillis();
        mGcWatcher = new WeakReference<GcWatcher>(new GcWatcher());
    }
}
```
