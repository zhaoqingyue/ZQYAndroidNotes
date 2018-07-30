### Android Activity

----Activity(活动)： Android 四大组件之一

> Activity --> ContextThemeWrapper --> ContextWrapper --> Context

- Activity用于显示用户界面，用户通过Activity交互完成相关操作 
- 一个App允许有多个Activity

**1. Activity概述**

- Activity：活动，在Android中代表手机的屏幕，是Android四大组件之一，提供了与用户交互的可视化界面，App由多个Activity组成；
- Android系统使用Task（栈）来存储Activity，可以理解为Activity栈，即先进后出。

----当一个Activity启动另一个Activity时，第二个Activity压入第一个Activity的栈中，此时两个Activity放在同一个Task中；当按下返回键时，第二个Activity从栈中弹出，第一个Activity回到栈顶，即显示到当前屏幕。

**2. Activity生命周期**

> 创建-onCreate --> 启动-onStart –-> 开始-onResume –-> 暂停-onPause –-> 结束-onStop –-> 销毁onDestroy

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E5%9B%9B%E5%A4%A7%E7%BB%84%E4%BB%B6/img/activity_lifecycle.png)

> 每一个活动（ Activity ）都处于某一个状态，对于开发者来说，是无法控制其应用程序处于某一个状态的，这些均由系统来完成。 但是当一个活动的状态发生改变的时候，开发者可以通过调用 onXX() 的方法获取到相关的通知信息

**2.1 生命周期的7个方法**
- onCreat：当Activity第一次启动时，触发该方法，可以在此时完成活动的初始化工作
- onStart：当Activity展现给用户（但不能交互）时，触发该方法
- onResume：当Activity和用户发生交互时，触发该方法
- onPause：当Activity正在前台运行，因为其他的活动需要前台运行而转入后台运行时，触发该方法（保存状态）
- onStop：当Activity不再需要展示给用户时，触发该方法
- onDestroy：当Activity销毁时，触发该方法
- onRestart ：当处于Stop状态的Activity需要再次展现给用户时，触发该方法

> onSaveInstanceState ：系统调用该方法，允许活动保存之前的状态


**2.2 Activity生命周期解析**
- a. 当Activity首次被创建时，会调用onCreate()方法；
- b. 当显示给用户时，调用onStart()；
- c. 当Activity位于前台时，调用onResume()方法，此时Activity位于栈顶；
- d. 当另一个Activity覆盖当前Activity时，调用onpause()方法，并将前一个Activity的数据保存起来；
- e. 当前一个Activity不再显示时，调用onStop()方法，停止该Activity；如果想再次回到前台，重新获取焦点，则调用onRestart()方法；
- f. onStop()后，可以调用onDestory()方法销毁Activity。此时是Activity最后一次被调用。可以通过finish()关闭Activity。
- g. 当内存资源不足时，可能杀死处于onStop()的Activity所在进程。

**3. Activity / ActionBarActivity / AppCompatActivity**
- ActionBarActivity / AppCompatActivity 是为了低版本兼容而提出的，都在v7包下；
- ActionBarActivity已被废弃，自带ActionBar。而在5.0后，被Google弃用了，现在用 ToolBar；
- 在Android Studio创建一个Activity默认继承的是：AppCompatActivity，AppCompatActivity给我们提供了一些新的东西。

**4. 启动模式launchMode**
- standard(默认)
- singleTop
- singleTask
- singleInstance 

**4.1 standard模式**

----每次启动Activity，都会创建一个新的Activity对象，即使有一个该Activity实例在栈顶

**4.2 singleTop模式**

----启动Activity时，如果有一个该Activity实例在栈顶，则直接复用已有Activity实例，不再创建新的Activity实例

**4.3 singleTask模式**

----在同一个Task中仅有一个该Activity实例

- 如果要启动的Activity不存在，则新建一个，并压入栈顶
- 如果存在，且位于栈顶，则不会创建新的实例，直接复用该Activity实例
- 如果存在，但不在栈顶，则移除该Activity上的其它Activity，从而让该Activity位于栈顶

**4.4 singleInstance模式**

----无论哪个Task启动Activity，都只会创建一个Activity实例，并将它加入到新的task栈顶

- 如果要启动的Activity不存在，则先创建一个新的task，然后创建Activity加入栈顶
- 如果已经存在，无论位于哪个task，都会移到前台，从而显示该Activity

> singleInstance模式启动的Activity总是位于栈顶，因为它所在的task只有它一个Activity

**5. Activity & Window & View**

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E5%9B%9B%E5%A4%A7%E7%BB%84%E4%BB%B6/img/activity1.png)

- Activity通过setContentView()加载布局
- 实际调用Window中的setContentView()
- PhoneWindow是Window的子类
- PhoneWindow中有一个DecorView的内部类，是所有应用窗口的根View，直接控制Activity是否显示
- DecorView里面有个LinearLayout
- LinearLayout下面有两个FrameLayout，分别是TitleView和CustomView
- setContentView()加载的布局放在CustomView上

[常用系统Activity](https://note.youdao.com/)



