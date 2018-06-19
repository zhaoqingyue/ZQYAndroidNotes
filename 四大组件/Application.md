### Application

**1. 定义**

- 代表应用程序（即 Android App）的类，也属于Android中的一个系统组件；
- 继承关系：继承自 ContextWarpper 类。

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E5%9B%9B%E5%A4%A7%E7%BB%84%E4%BB%B6/img/application0.png)

- 说明：

----Application和Activity，Service一样，是android框架的一个系统组件。

当android程序启动时，系统会创建一个 application对象，用来存储系统的一些信息。通常是不需要指定一个Application的，系统会自动创建。

如果需要创建自己的Application，也很简单：创建一个类继承 Application，并在manifest的application标签中进行注册(只需要给Application标签增加个name属性把自己的 Application的名字定入即可)。

**自定义Application：**

```
public class MyApplication extends Application { 
    private static Context mContext; 
    
    private static MyApplication app;  
      
    public static MyApplication getInstance() {  
        return app;  
    }  
    
    @Override public void onCreate() { 
        super.onCreate(); 
        app = this;  
        mContext = getApplicationContext(); 
    } 
    
    public static Context getInstance() { 
        return mContext; 
        
    } 
}

```

**在AndroidManifest文件中指定自定义的Application**

```
 <application 
    android:name=".MyApplication" 
    android:allowBackup="true" 
    android:icon="@mipmap/ic_launcher" 
    android:label="@string/app_name" 
    android:supportsRtl="true" 
    android:theme="@style/AppTheme" />

```

**2. 特点**

**（1）实例创建方式：单例模式**

- 每个Android App运行时，会首先自动创建Application类，并实例化Application 对象，且只有一个（即 Application类 是单例模式（singleton）类）；

- 也可通过继承Application类，自定义Application 类和实例。

**（2）实例形式：全局实例**

----不同的组件（如Activity、Service）都可获得Application对象，且都是同一个对象。

**（3）生命周期**

----等于 Android App 的生命周期。Application 对象的生命周期是整个程序中最长的，等于Android App的生命周期。

**3. 方法介绍**

Application方法执行顺序:

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E5%9B%9B%E5%A4%A7%E7%BB%84%E4%BB%B6/img/application3.png)


（1）onCreate（）

- 调用时刻

----Application 实例创建时调用。

### Android系统的入口是Application类的 onCreate（），默认为空实现。

- 作用

A. 初始化应用程序级别的资源，如全局对象、环境配置变量、图片资源初始化、推送服务的注册等；

注：请不要执行耗时操作，否则会拖慢应用程序启动速度。

B. 数据共享、数据缓存。设置全局共享数据，如全局共享变量、方法等。

注：这些共享数据只在应用程序的生命周期内有效，当该应用程序被杀死，这些数据也会被清空，所以只能存储一些临时性的共享数据。

- 具体使用

```
// 复写方法需要在Application子类里实现

private static final String VALUE = "zhaoqy";

// 初始化全局变量
@Override
public void onCreate() {
    super.onCreate();
    VALUE = "zhaoqy_demo";
}

```

（2）registerComponentCallbacks（） & unregisterComponentCallbacks（）

- 作用

----注册和注销 ComponentCallbacks2回调接口。

- 具体使用

```
registerComponentCallbacks(new ComponentCallbacks2() {

    // 接口里方法下面会继续介绍
    @Override
    public void onTrimMemory(int level) {

    }

    @Override
    public void onLowMemory() {

    }

    @Override
    public void onConfigurationChanged(Configuration newConfig) {

    }
});
```

（3）onTrimMemory（）

- 作用

----通知 应用程序 当前内存使用情况（以内存级别进行识别）。

注：onTrimMemory（）是Android 4.0 后提供的API。

- 应用场景

----根据当前内存使用情况进行自身的内存资源的不同程度释放，以避免被系统直接杀掉 & 优化应用程序的性能体验。

- 具体使用

```
registerComponentCallbacks(new ComponentCallbacks2() {

    @Override
    public void onTrimMemory(int level) {

    // Android系统会根据当前内存使用的情况，传入对应的级别
    // 下面以清除缓存为例子介绍
    super.onTrimMemory(level);
    if (level >= ComponentCallbacks2.TRIM_MEMORY_MODERATE) {
        mPendingRequests.clear();
        mBitmapHolderCache.evictAll();
        mBitmapCache.evictAll();
    }
});

```

（4）onLowMemory()

- 作用

----监听 Android系统整体内存较低时刻。

- 调用时刻

----Android系统整体内存较低时。

```
registerComponentCallbacks(new ComponentCallbacks2() {

    @Override
    public void onLowMemory() {

    }
});

```

- 应用场景

----Android 4.0前 检测内存使用情况，从而避免被系统直接杀掉 & 优化应用程序的性能体验。

（5）ConfigurationChanged（）

- 作用

----监听 应用程序 配置信息的改变，如屏幕旋转等。

配置信息是指Manifest.xml文件下的Activity标签属性android:configChanges的值，如下：

```
<activity android:name=".MainActivity">
    android:configChanges="keyboardHidden|orientation|screenSize"
    // 设置该配置属性会使Activity在配置改变时不重启，只执行onConfigurationChanged（）
    // 上述语句表明，设置该配置属性可使 Activity 在屏幕旋转时不重启
</activity>
 
```

- 调用时刻

----应用程序配置信息 改变时调用

- 具体使用

```
registerComponentCallbacks(new ComponentCallbacks2() {

    @Override
    public void onConfigurationChanged(Configuration newConfig) {
        ...
    }
});

```

（6）onTerminate（）
 
-  调用时刻

----应用程序结束时调用。

该方法只用于Android仿真机测试，在Android产品机是不会调用的。

4. 总结

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E5%9B%9B%E5%A4%A7%E7%BB%84%E4%BB%B6/img/application2.png)