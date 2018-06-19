### Service

----服务， Android 四大组件之一。

**1. 作用**

----提供需要在后台长期运行的服务（如复杂计算、音乐播放、下载等）。

**2. 特点**

- 生命周期长
- 没有用户界面
- 在后台运行

**3. 生命周期**

官方说明图：

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E5%9B%9B%E5%A4%A7%E7%BB%84%E4%BB%B6/img/service0.png)

两种开启Service的方法：

- 直接开启startService，使用stopService关闭

----stratService和stopService一一对应，一个开启，一个结束。

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E5%9B%9B%E5%A4%A7%E7%BB%84%E4%BB%B6/img/service1.png)

- 绑定开启bindService，使用unbindService解绑关闭

----bindServic和unbindService一一对应，一个绑定开启，一个解绑结束。

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E5%9B%9B%E5%A4%A7%E7%BB%84%E4%BB%B6/img/service2.png)

在service生命周期里，常用的方法有：

- 4个手动调用的方法

手动调用方法 | 作用
---|---
startService() | 启动服务
stopService() | 关闭服务
bindService() | 绑定服务
unbindService() | 解绑服务

**调用判定条件：**

- startService()

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E5%9B%9B%E5%A4%A7%E7%BB%84%E4%BB%B6/img/service3.png)

- stopService()

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E5%9B%9B%E5%A4%A7%E7%BB%84%E4%BB%B6/img/service4.png)

- bindService()

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E5%9B%9B%E5%A4%A7%E7%BB%84%E4%BB%B6/img/service5.png)

- unbindService()

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E5%9B%9B%E5%A4%A7%E7%BB%84%E4%BB%B6/img/service6.png)

**特别注意：**

- startService() 和 stopService()只能开启 & 关闭，但无法操作service；
- bindService() 和 unbindService()除了绑定 & 解除，还可以操作service；
- startService()开启的service，调用者退出后，service仍然存在；
- bindService()开启的service，调用者退出后，service随着调用者销毁。


- 5个自动调用的方法

自动调用的方法 | 作用
---|---
onCreat() | 创建服务
onStartCommand() | 开始服务
onDestroy() | 销毁服务
onBind() | 绑定服务
onUnbind() | 解绑服务

**特别注意：**

- 在整个生命周期内，只有startCommand()能被多次调用。其他方法只能被调用一次。（即只能绑定和解绑一次）

- 绑定后没有解绑，无法使用stopService()将其停止。

- 如果已经onCreate()，那么startService()将只调用startCommand()。

- 如果是以bindService开启，那么使用unbindService时就会自动调用onDestroy销毁。


**4. Service分类**

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E5%9B%9B%E5%A4%A7%E7%BB%84%E4%BB%B6/img/service7.png)

**5. 使用讲解**

（1）本地Service

----最普通、最常用的后台服务Service。只能单机使用，无法与Activity通信。

使用步骤

- 步骤1：新建子类继承Service类

----需重写父类的onCreate()、onStartCommand()、onDestroy()和onBind()方法

```
public class MyService extends Service {

    //启动Service之后，就可以在onCreate()或onStartCommand()方法里去执行一些具体的逻辑
    @Override
    public void onCreate() {
        super.onCreate();
        System.out.println("执行了onCreat()");
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        System.out.println("执行了onStartCommand()");
        return super.onStartCommand(intent, flags, startId);
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        System.out.println("执行了onDestory()");
    }

    @Nullable
    @Override
    public IBinder onBind(Intent intent) {
        return null;
    }
}

```

- 步骤2：构建用于启动Service的Intent对象

- 步骤3：调用startService()启动Service、调用stopService()停止服务

```
//构建启动服务的Intent对象
Intent startIntent = new Intent(this, MyService.class);
//调用startService()方法-传入Intent对象,以此启动服务
startService(startIntent);
                
//构建停止服务的Intent对象
Intent stopIntent = new Intent(this, MyService.class);
//调用stopService()方法-传入Intent对象,以此停止服务
stopService(stopIntent);

```

- 步骤4：在AndroidManifest.xml里注册Service

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="scut.carson_ho.demo_service">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        
        ......

        //注册Service服务
        <service android:name=".MyService">
        </service>

    </application>

</manifest>
```

(2) 可通信的服务Service

----在基础用法上，增设“与Activity通信”的功能，即使用绑定Service服务（Binder类、bindService()、onBind(）、unbindService()、onUnbind()）

- 步骤1：在新建子类继承Service类，并新建一个子类继承自Binder类、写入与Activity关联需要的方法、创建实例


```
public class MyService extends Service {

    private MyBinder mBinder = new MyBinder();

    @Override
    public void onCreate() {
        super.onCreate();
        System.out.println("执行了onCreat()");
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        System.out.println("执行了onStartCommand()");
        return super.onStartCommand(intent, flags, startId);
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        System.out.println("执行了onDestory()");
    }

    @Nullable
    @Override
    public IBinder onBind(Intent intent) {
        System.out.println("执行了onBind()");
        //返回实例
        return mBinder;
    }


    @Override
    public boolean onUnbind(Intent intent) {
        System.out.println("执行了onUnbind()");
        return super.onUnbind(intent);
    }

    //新建一个子类继承自Binder类
    class MyBinder extends Binder {

        public void service_connect_Activity() {
            System.out.println("Service关联了Activity,并在Activity执行了Service的方法");
        }
    }
}

```

- 在Activity通过调用MyBinder类中的public方法来实现Activity与Service的联系


```
private MyService.MyBinder myBinder;


//创建ServiceConnection的匿名类
private ServiceConnection connection = new ServiceConnection() {

    //重写onServiceConnected()方法和onServiceDisconnected()方法
    //在Activity与Service建立关联和解除关联的时候调用
    @Override
    public void onServiceDisconnected(ComponentName name) {
    
    }

    //在Activity与Service解除关联的时候调用
    @Override
    public void onServiceConnected(ComponentName name, IBinder service){
        //实例化Service的内部类myBinder
        //通过向下转型得到了MyBinder的实例
        myBinder = (MyService.MyBinder) service;
        //在Activity调用Service类的方法
        myBinder.service_connect_Activity();
    }
};


//构建绑定服务的Intent对象
Intent bindIntent = new Intent(this, MyService.class);
//调用bindService()方法,以此停止服务

bindService(bindIntent,connection,BIND_AUTO_CREATE);
//参数说明
//第一个参数:Intent对象
//第二个参数:上面创建的Serviceconnection实例
//第三个参数:标志位
//这里传入BIND_AUTO_CREATE表示在Activity和Service建立关联后自动创建Service
//这会使得MyService中的onCreate()方法得到执行，但onStartCommand()方法不会执行

//调用unbindService()解绑服务
//参数是上面创建的Serviceconnection实例
unbindService(connection);

```

（3） 前台Service

----前台Service和后台Service（普通）最大的区别就在于：

- 前台Service在下拉通知栏有显示通知，但后台Service没有；

- 前台Service优先级较高，不会由于系统内存不足而被回收；后台Service优先级较低，当系统出现内存不足情况时，很有可能会被回收

**具体使用**

----在原有的Service类对onCreate()方法进行稍微修：

```
@Override
public void onCreate() {
    super.onCreate();
    System.out.println("执行了onCreat()");

    //添加下列代码将后台Service变成前台Service
    //构建"点击通知后打开MainActivity"的Intent对象
    Intent notificationIntent = new Intent(this,MainActivity.class);
    PendingIntent pendingIntent = PendingIntent.getActivity(this,0,notificationIntent,0);

    //新建Builer对象
    Notification.Builder builer = new Notification.Builder(this);
    builer.setContentTitle("前台服务通知的标题");//设置通知的标题
    builer.setContentText("前台服务通知的内容");//设置通知的内容
    builer.setSmallIcon(R.mipmap.ic_launcher);//设置通知的图标
    builer.setContentIntent(pendingIntent);//设置点击通知后的操作

    Notification notification = builer.getNotification();//将Builder对象转变成普通的notification
    startForeground(1,notification);//让Service变成前台Service,并在系统的状态栏显示出来

    }

```

（4）远程Service