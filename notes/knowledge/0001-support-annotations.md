# Support Annotations库介绍

### 0. 引子
从Android Support Library v19.1开始，Support库新增了一个annotations子库，里面定义了一些annotation。这些annotation可以被用于在代码中强制添加一些调用约束，进而便于IDE对代码进行静态检查，以发现潜在的问题。

关于该库的官方介绍和用法，请看 [这里](http://tools.android.com/tech-docs/support-annotations).

下面简单总结一下其用法。

### 1. 添加库的引用
#### 方式一：
直接修改build.gradle配置文件，添加该库的引用。例如：
```
dependencies {
    compile 'com.android.support:support-annotations:21.0.3'
}
```

#### 方式二：
通过Android Studio的Project Structure（File -> Project Structure，Dependencies）添加引用。

### 2. 包含的annotations介绍
#### 2.1 null相关
用于修饰Method的参数和返回值，以便IDE对相关的约束进行检查。列举如下：
* @Nullable 表示允许为null
* @NonNull 表示不允许为null

从官网抄一段例子：
```java
import android.support.annotation.NonNull;
import android.support.annotation.Nullable;
...

    /**
     * Add support for inflating the &lt;fragment> tag.
     */
    @Nullable
    @Override
    public View onCreateView(String name, @NonNull Context context, @NonNull AttributeSet attrs) {
        ...
```

#### 2.2 资源类型相关
用于修饰对资源ID的相关引用，以便IDE对相关的资源类型进行检查。列举如下：
* @AnimRes
* @AnimatorRes
* @AnyRes
* @ArrayRes
* @AttrRes
* @BoolRes
* @ColorRes
* @DimenRes
* @DrawableRes
* @FractionRes
* @IdRes
* @IntegerRes
* @InterpolatorRes
* @LayoutRes
* @MenuRes
* @PluralsRes
* @RawRes
* @StringRes
* @StyleRes
* @StyleableRes
* @XmlRes

从官网抄一段例子：
```java
import android.support.annotation.StringRes;
...
    public abstract void setTitle(@StringRes int resId);
```

#### 2.3 常量定义相关
##### @IntDef
我们经常使用int来定义一些常量来代替使用enum，可以使用这个annotation来添加相关的约束。

还是搬官网的例子：
```java
import android.support.annotation.IntDef;
...
public abstract class ActionBar {
    ...
    @Retention(RetentionPolicy.SOURCE)
    @IntDef({NAVIGATION_MODE_STANDARD, NAVIGATION_MODE_LIST, NAVIGATION_MODE_TABS})
    public @interface NavigationMode {}

    public static final int NAVIGATION_MODE_STANDARD = 0;
    public static final int NAVIGATION_MODE_LIST = 1;
    public static final int NAVIGATION_MODE_TABS = 2;

    @NavigationMode
    public abstract int getNavigationMode();

    public abstract void setNavigationMode(@NavigationMode int mode);
```

再搬一个例子，关于flags类型(可进行异或运算)的常量定义：
```java
    @IntDef(flag=true, value={
            DISPLAY_USE_LOGO,
            DISPLAY_SHOW_HOME,
            DISPLAY_HOME_AS_UP,
            DISPLAY_SHOW_TITLE,
            DISPLAY_SHOW_CUSTOM
    })
    @Retention(RetentionPolicy.SOURCE)
    public @interface DisplayOptions {}
```

##### @StringDef
该annotation用于修饰字符串常量，用法跟前面的@IntDef类似。

### 3. 结语
在项目中支持这些annotation是比较繁琐的，就像为项目添加单元测试的支持一样。但同样的，如果能够在项目中支持这些annotation，其带来的质量提升是很明显的。特别是对于library project项目，建议在代码规范予以支持。
