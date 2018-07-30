### Activity的使用

**1. 自定义Activity类名，继承Activity类或其它子类**
```
public class MainActivity extends Activity {
    ...
}
```

**2. 重写onCreate()方法，并调用setContentView()方法设置要显示的视图**
```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    ...
}
```

Android 5.0(API版本 >= 21)提供的新方法
```
@Override
public void onCreate(Bundle savedInstanceState, PersistableBundle persistentState) {
    super.onCreate(savedInstanceState, persistentState);
    setContentView(R.layout.activity_main);
    ...
}
```
要用新的方法，先在Androidmanifest.xml中为Activity设置一个属性
```
android:persistableMode="persistAcrossReboots"
```

使用新方法后， Activity拥有了持久化的能力
```
/**
 * 调用场景：
 * 1. 点击home键回到主页或长按后选择运行其他程序
 * 2. 按下电源键关闭屏幕
 * 3. 启动新的Activity
 * 4. 横竖屏切换
 */
public void onSaveInstanceState(Bundle outState, PersistableBundle outPersistentState)

/**
 * 取出onSaveInstanceState()保存的数据
 */
public void onRestoreInstanceState(Bundle savedInstanceState, PersistableBundle persistentState)
```

**3. 在Androidmanifest.xml中声明Activity**
```
<application
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:supportsRtl="true"
    android:theme="@style/AppTheme">

    <activity 
        android:name=".MainActivity类名"
        android:label="xxxActivity显示的标题"
        android:theme="主题">
    </activity>
       
</application>
```

**4. 启动Activity**

----在Android中可以通过两种方式来启动一个新的Activity：显示启动 和 隐式启动

> 注意：这里是启动方式，而不是启动模式

**4.1 显示启动**

**4.1.1 常见方式**
```
Intent intent = new Intent(A.this, B.class);
startActivity(intent);
// 或者
startActivityForResult(intent, requestCode);
```

**4.1.2 通过Intent的ComponentName**
```
// ComponentName cn = new ComponentName("启动应用的包名", "启动Activity的类名（包名+类名）");
ComponentName cn = new ComponentName("com.zqy.test", "com.zqy.test.MainActivity");
Intent intent = new Intent() ;
intent.setComponent(cn) ;
startActivity(intent) ;
```

**4.1.3 初始化Intent时指定包名**
```
Intent intent = new Intent("android.intent.action.MAIN");
intent.addCategory(Intent.CATEGORY_LAUNCHER);
//参数是包名，类全限定名
ComponentName cn=new ComponentName("com.zqy.test", "com.zqy.test.MainActivity");
intent.setComponent(cn);
startActivity(intent);
```

**4.2 隐式启动**

**4.2.1 根据Action和Category信息来进行匹配**

在AndroidManifest.xml中注册TestActivity
```
<activity 
    android:name=".TestActivity"  
    android:label="TestActivity" >
        <intent-filter >
            <action android:name="com.zqy.test"/>
            <category android:name="android.intent.category.DEFAULT"/>
        </intent-filter>
</activity>
```

在MainActivity中启动TestActivity
```
Intent intent = new Intent();
// 设置intent的Action属性值
intent.setAction("com.zqy.test");
// 不加这行也行，因为这个值默认就是Intent.CATEGORY_DEFAULT
intent.addCategory(Intent.CATEGORY_DEFAULT);
startActivity(intent);
```

**4.2.2 根据Action和Data信息进行匹配**

在AndroidManifest.xml中注册TestActivity
```
<activity 
    android:name=".TestActivity"  
    android:label="TestActivity" >
        <intent-filter >
            <action android:name="com.zqy.test"/>
            <category android:name="android.intent.category.DEFAULT"/>
            <data android:scheme="zqytest"/>
        </intent-filter>
</activity>
```

在MainActivity中启动TestActivity
```
Uri uri = Uri.parse("zqytest://");
// 或者
Uri uri = Uri.parse("zqytest:");	

Intent intent = new Intent();
// 设置intent的Action属性值
intent.setAction("com.zqy.test");
// 不加这行也行，因为这个值默认就是Intent.CATEGORY_DEFAULT
intent.addCategory(Intent.CATEGORY_DEFAULT);
// 设置data属性
intent.setData(uri);
startActivity(intent);
```

**4.2.3 根据action和data的mimeType属性匹配**

在AndroidManifest.xml中注册TestActivity
```
<activity 
    android:name=".TestActivity"  
    android:label="TestActivity" >
        <intent-filter >
            <action android:name="com.zqy.test"/>
            <category android:name="android.intent.category.DEFAULT"/>
            <data android:mimeType="vnd.android.cursor.dir/vnd.google.note" />
        </intent-filter>
</activity>
```

在MainActivity中启动TestActivity
```
Intent intent = new Intent();
intent .setAction("android.intent.action.VIEW");
intent .addCategory(Intent.CATEGORY_DEFAULT);
// 要设置Data的MIMEType属性
intent.setType("vnd.android.cursor.dir/vnd.google.note");
startActivity(intent );
```

**5. 关闭Activity**
```
finish();
```

**5.1 关闭所有Activity**
- 创建Activity管理类，定义Activity管理方法
```
public class ActivityCollector {  

    public static LinkedList<Activity> activities = new LinkedList<Activity>();  
    
    public static void addActivity(Activity activity) {  
        activities.add(activity);  
    }  
    
    public static void removeActivity(Activity activity) {  
        activities.remove(activity);  
    }  
    
    public static void finishAll() {  
        for(Activity activity:activities) {  
            if(!activity.isFinishing()) {  
                activity.finish();  
            }  
        }  
    }  
}  
```
- 创建BaseActivity
    - 在onCreate()中添加：ActivityCollector.addActivity(this)
    - 在onDestory()中添加：ActivityCollector.removeActivity(this)
    - 在任意位置调用finishAll()，关闭所有Activity，从而退出App
- 继承BaseActivity

**5.2 完全退出App（杀死App进程）**
```
public void AppExit(Context context) {  
    try {  
        ActivityCollector.finishAll();  
        ActivityManager activityMgr = (ActivityManager) context  
                .getSystemService(Context.ACTIVITY_SERVICE);  
        activityMgr.killBackgroundProcesses(context.getPackageName());  
        System.exit(0);  
    } catch (Exception ignored) {
        
    }  
}  
```

**5.3 双击退出App**

**5.3.1 定义一个变量，标识是否退出**

```
// 定义一个变量，来标识是否退出
private static boolean isExit = false;

Handler mHandler = new Handler() {

    @Override
    public void handleMessage(Message msg) {
        super.handleMessage(msg);
        isExit = false;
    }
};

public boolean onKeyDown(int keyCode, KeyEvent event) {
    if (keyCode == KeyEvent.KEYCODE_BACK) {
        if (!isExit) {
            isExit = true;
            Toast.makeText(getApplicationContext(), "再按一次退出程序", Toast.LENGTH_SHORT).show();
            // 利用handler延迟发送更改状态信息
            mHandler.sendEmptyMessageDelayed(0, 2000);
        } else {
            finish();
        }
        return false;
    }
    return super.onKeyDown(keyCode, event);
}
```

**5.3.2 保存点击时间**
```
// 保存点击的时间
private long exitTime = 0;

public boolean onKeyDown(int keyCode, KeyEvent event) {
    if (keyCode == KeyEvent.KEYCODE_BACK) {
        if ((System.currentTimeMillis() - exitTime) > 2000) {
            Toast.makeText(getApplicationContext(), "再按一次退出程序", Toast.LENGTH_SHORT).show();
            exitTime = System.currentTimeMillis();
        } else {
            finish();
        }
        return false;
    }
    return super.onKeyDown(keyCode, event);
}
```

**6. 数据传递**

**6.1 Activity间数据传递**

- 一个个传
```
// 传数据
Intent intent = new Intent(A.this, B_Activity.class);
intent.putExtra("age", 18);
startActivity(intent);

// 获取数据
Intent intent = getIntent();
int age = intent.getIntExtra("age");
```

- 一次传多个
```
// 传数据
Intent intent = new Intent(A.this, B_Activity.class);
Bundle bundle = new Bundle();
bundle.putInt("age", 18);
bundle.putString("name", "zhaoqy");
intent.putExtras(bundle);
startActivity(intent);

// 获取数据
Intent intent = getIntent();
Bundle bundle = intent.getExtras();
int age = bundle.getInt("age");
String name = bundle.getString("name");
```

**6.2 多个Activity间的交互**

- 启动Activity
```
Intent intent = new Intent(A.this, B_Activity.class);
startActivityForResult(intent, requestCode);
```
- 在A Activity中重写onActivityResult()
```
public void onActivityResult(int requestCode, int resultCode, Intent data) {
    ...
}
```
- 在启动的B Activity中调用setResult()
```
Intent intent = getIntent();
setResult(resultCode, intent);
```

**7. 横竖屏切换与状态保存**

**7.1 屏幕横竖屏切换**
```
 android:screenOrientation=""
 // 属性值
 - unspecified: 默认值，由系统来判断显示方向
 - landscape: 横屏显示
 - portrait: 竖屏显示
 - user: 用户当前首选的方向
 - behind: 和该Activity下面的Activity方向一致
 - sensor: 由物理感应器来决定。如果用户旋转设备，屏幕会横竖屏切换。
 - nosensor: 忽略物理感应器
```

**7.2 加载不同的布局**

- A. 准备两套不同的布局，Android根据横竖屏加载不同布局
    - 创建两个布局文件夹：layout-land横屏 & layout-port竖屏
    - 把这两套布局文件放在两文件夹里，文件名一样
    - Android自行判断，加载相应布局

- B. 代码中进行判断，在onCreate()方法中加载布局文件

对横竖屏的状态做下判断，关键代码如下：
```
int orientation = getResources().getConfiguration().orientation;
if (orientation == Configuration.ORIENTATION_LANDSCAPE) {  
    setContentView(R.layout.landscape);
} else if (orientation == Configuration.ORIENTATION_PORTRAIT) {  
    setContentView(R.layout.portrait);
}
```

**7.3 状态保存**

----通过Bundle savedInstanceState参数即可完成

**三个核心方法**
- onCreate(Bundle savedInstanceState)
- onSaveInstanceState(Bundle outState)
- onRestoreInstanceState(Bundle savedInstanceState)

**7.3.1 重写onSaveInstanceState()方法，往bundle中写入数据**
```
outState.putInt("num",1);
```

**7.3.2 在onCreate或者onRestoreInstanceState中获取存储的数据**
```
// 获取之前，判断savedInstanceState是否为null
savedInstanceState.getInt("num");
```

**8. 设置Activity全屏**

**8.1 代码隐藏ActionBar**
```
getActionBar.hide();
```

**8.2 设置requestWindowFeature**
```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    requestWindowFeature(Window.FEATURE_NO_TITLE);
    setContentView(R.layout.activity_main);
    ...
}
```
> requestWindowFeature在setContentView()之前

**8.3 设置AndroidManifest.xml的theme**
```
<activity 
    android:name=".MainActivity"
    android:label="标题"
    android:theme=@android:style/Theme.NoTitleBar.FullScreen >
</activity>
```

