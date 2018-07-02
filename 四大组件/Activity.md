### Android Activity

----Activity(活动)： Android 四大组件之一。

- Activity用于显示用户界面，用户通过Activity交互完成相关操作 
- 一个App允许有多个Activity

**1.1. 什么是Activity？**

- Activity：活动，在Android中代表手机的屏幕，是Android四大组件之一，提供了与用户交互的可视化界面，App由多个Activity组成；
- Android系统使用Task（栈）来存储Activity，可以理解为Activity栈，即先进后出。

----当一个Activity启动另一个Activity时，第二个Activity压入第一个Activity的栈中，此时两个Activity放在同一个Task中；当按下返回键时，第二个Activity从栈中弹出，第一个Activity回到栈顶，即显示到当前屏幕。

**1.2 Activity生命周期解析**

- a. 当Activity首次被创建时，会调用onCreate()方法；
- b. 当显示给用户时，调用onStart()；
- c. 当Activity位于前台时，调用onResume()方法，此时Activity位于栈顶；
- d. 当另一个Activity覆盖当前Activity时，调用onpause()方法，并将前一个Activity的数据保存起来；
- e. 当前一个Activity不再显示时，调用onStop()方法，停止该Activity；如果想再次回到前台，重新获取焦点，则调用onRestart()方法；
- f. onStop()后，可以调用onDestory()方法销毁Activity。此时是Activity最后一次被调用。可以通过finish()关闭Activity。
- g. 当内存资源不足时，可能杀死处于onStop()的Activity所在进程。

**2. Activity / ActionBarActivity / AppCompatActivity**

- ActionBarActivity / AppCompatActivity 是为了低版本兼容而提出的，都在v7包下；
- ActionBarActivity已被废弃，自带ActionBar。而在5.0后，被Google弃用了，现在用 ToolBar；
- 在Android Studio创建一个Activity默认继承的是：AppCompatActivity，AppCompatActivity给我们提供了一些新的东西。

**3. Activity的创建流程**

```
public class A extend Activity {
    
    @override
    public void onCreate() {
        
    }
}
```

**4. 启动Activity的方式**

----在Android中可以通过两种方式来启动一个新的Activity：显示启动 和 隐式启动。

注意：这里是启动方式，而不是启动模式。

**4.1 显示启动：通过报名来启动**

- A. 常见方式
```
Intent intent = new Intent(A.this, B.class);
startActivity(intent);
```

- B. 通过Intent的ComponeName

```
ComponentName cn = new ComponentName(A.class, B.class) ;
Intent intent = new Intent() ;
intent.setComponent(cn) ;
startActivity(intent) ;
```

- C. 初始化Intent时指定包名

```
Intent intent = new Intent("android.intent.action.MAIN");
intent.setClassName("当前Act的全限定类名","启动Act的全限定类名");
startActivity(intent);
```

**4.2 隐式启动：通过intent-filter的Action，Category或data来实现**
 
