### BroadcastReceiver
----广播接收器，属于 Android 四大组件之一。

**1. 定义**

----广播，是一个全局的监听器。

**2. 作用**

----监听 / 接收 应用 App 发出的广播消息，并做出响应。

**3. 应用场景**
- Android不同组件间的通信（应用内 / 不同应用之间）；
- 多线程通信；
- 与 Android 系统在特定情况下的通信（如：电话呼入时、网络可用时）。

**4. 实现原理**

----Android中的广播使用了设计模式中的观察者模式：基于消息的发布 / 订阅事件模型

广播中的3个角色：
- 消息订阅者（广播接收者）
- 消息发布者（广播发布者）
- 消息中心（AMS，即Activity Manager Service）

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E5%9B%9B%E5%A4%A7%E7%BB%84%E4%BB%B6/img/broadcast0.png)

原理描述：

- 广播接收者通过Binder机剧在 AMS 注册；
- 广播发送者通过Binder机制向 AMS 发送广播；
- AMS 根据广播发送者要求，在已注册列表中，寻找合适的广播接收者（导找依据：IntentFilter / Permission ) 
- AMS 将广播发送到合适的广播接收者相应的消息循环队列中；
- 广播接收者通过消息循环，得到此广播，并回调onReceive（）；

特别注意：广播发送者和广播接收者的执行是"异步"的，即广播发送者不会关心有无接收者接收 ＆ 也不确定接收者何时才能接收到。

**5. 使用流程**

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E5%9B%9B%E5%A4%A7%E7%BB%84%E4%BB%B6/img/broadcast1.png)

（1）**自定义广播接收者BroadcastReceiver**
- 继承BroadcastReceivre基类
- 复写抽象方法onReceive()

说明：
- 广播接收器接收到相应广播后，会自动回调 onReceive() 方法；
- 一般情况下，onReceive方法会涉及 与 其他组件之间的交互，如发送Notification、启动Service等；
- 默认情况下，广播接收器运行在 UI 线程，因此，onReceive()方法不能执行耗时操作，否则将导致ANR。

```
// 继承BroadcastReceivre基类
public class XXBroadcastReceiver extends BroadcastReceiver {

  // 复写onReceive()方法
  // 接收到广播后，则自动调用该方法
  @Override
  public void onReceive(Context context, Intent intent) {
   //写入接收广播后的操作
  }
}
```

**（2）广播接收器注册**

----广播的注册方式分为两种：静态注册、动态注册。

- 静态注册

A. 注册方式：在AndroidManifest.xml里通过<receive>标签声明

B. 属性说明：

```
<receiver 
    android:enabled=["true" | "false"]
//此broadcastReceiver能否接收其他App的发出的广播
//默认值是由receiver中有无intent-filter决定的：如果有intent-filter，默认值为true，否则为false
    android:exported=["true" | "false"]
    android:icon="drawable resource"
    android:label="string resource"
//继承BroadcastReceiver子类的类名
    android:name=".mBroadcastReceiver"
//具有相应权限的广播发送者发送的广播才能被此BroadcastReceiver所接收；
    android:permission="string"
//BroadcastReceiver运行所处的进程
//默认为app的进程，可以指定独立的进程
//注：Android四大基本组件都可以通过此属性指定自己的独立进程
    android:process="string" >

//用于指定此广播接收器将接收的广播类型
//本示例中给出的是用于接收网络状态改变时发出的广播
    <intent-filter>
       <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
    </intent-filter>
</receiver>
```
C. 注册示例

```
<receiver 
    //此广播接收者类是XXBroadcastReceiver
    android:name=".XXBroadcastReceiver" >
    //用于接收网络状态改变时发出的广播
    <intent-filter>
        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
    </intent-filter>
</receiver>
```
当此 App首次启动时，系统会自动实例化XXBroadcastReceiver类，并注册到系统中。

- 动态注册

A. 注册方式：在代码中调用Context.registerReceiver（）方法

B. 具体代码如下：

```
// 选择在Activity生命周期方法中的onResume()中注册
@Override
protected void onResume(){
    super.onResume();

    // 1. 实例化BroadcastReceiver子类 &  IntentFilter
    XXBroadcastReceiver mBroadcastReceiver = new XXBroadcastReceiver();
    IntentFilter intentFilter = new IntentFilter();

    // 2. 设置接收广播的类型
    intentFilter.addAction(android.net.conn.CONNECTIVITY_CHANGE);

    // 3. 动态注册：调用Context的registerReceiver（）方法
    registerReceiver(mBroadcastReceiver, intentFilter);
 }


// 注册广播后，要在相应位置记得销毁广播
// 即在onPause() 中unregisterReceiver(mBroadcastReceiver)
// 当此Activity实例化时，会动态将MyBroadcastReceiver注册到系统中
// 当此Activity销毁时，动态注册的MyBroadcastReceiver将不再接收到相应的广播。
 @Override
 protected void onPause() {
    super.onPause();
    // 销毁在onResume()方法中的广播
    unregisterReceiver(mBroadcastReceiver);
}
```

**特别注意**

- 动态广播最好在Activity 的 onResume()注册、onPause()注销。
- 原因：

A. 对于动态广播，有注册就必然得有注销，否则会导致内存泄露(重复注册、重复注销也不允许)

B. Activity生命周期的方法是成对出现的：
- onCreate() & onDestory()
- onStart() & onStop()
- onResume() & onPause()

**两种注册方式的区别**

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E5%9B%9B%E5%A4%A7%E7%BB%84%E4%BB%B6/img/broadcast2.png)

**（3）广播发送者向AMS发送广播**

A. 广播的发送
- 广播是用”意图（Intent）“标识
- 定义广播的本质 = 定义广播所具备的“意图（Intent）”
- 广播发送 = 广播发送者 将此广播的“意图（Intent）”通过sendBroadcast（）方法发送出去

B. 广播的类型

广播的类型主要分为5类：

- 普通广播（Normal Broadcast）
- 系统广播（System Broadcast）
- 有序广播（Ordered Broadcast）
- 粘性广播（Sticky Broadcast）
- App应用内广播（Local Broadcast）

具体说明如下：

**a. 普通广播（Normal Broadcast）**

----即开发者自定义 intent的广播（最常用）。发送广播使用如下：

```
Intent intent = new Intent();
//对应BroadcastReceiver中intentFilter的action
intent.setAction(BROADCAST_ACTION);
//发送广播
sendBroadcast(intent);
```
若被注册了的广播接收者中注册时intentFilter的action与上述匹配，则会接收此广播（即进行回调onReceive()）。如下mBroadcastReceiver则会接收上述广播。

```
<receiver 
    //此广播接收者类是XXBroadcastReceiver
    android:name=".XXBroadcastReceiver" >
    //用于接收网络状态改变时发出的广播
    <intent-filter>
        <action android:name="BROADCAST_ACTION" />
    </intent-filter>
</receiver>

```
注意：若发送广播有相应权限，那么广播接收者也需要相应权限。

**b. 系统广播（System Broadcast）**
- Android中内置了多个系统广播：只要涉及到手机的基本操作（如开机、网络状态变化、拍照等等），都会发出相应的广播
- 每个广播都有特定的Intent - Filter（包括具体的action），Android系统广播action如下：

系统操作 | action
---|---
监听网络变化 | 	android.net.conn.CONNECTIVITY_CHANGE
关闭或打开飞行模式 | Intent.ACTION_AIRPLANE_MODE_CHANGED
充电时或电量发生变化 | Intent.ACTION_BATTERY_CHANGED
电池电量低 | Intent.ACTION_BATTERY_LOW
电池电量充足 | Intent.ACTION_BATTERY_OKAY
当前设置被改变时(语言、方向) | Intent.ACTION_CONFIGURATION_CHANGED
插入耳机 | Intent.ACTION_HEADSET_PLUG
成功安装APK | Intent.ACTION_PACKAGE_ADDED
成功删除APK | Intent.ACTION_PACKAGE_REMOVED
成功更新APK | android.intent.action.PACKAGE_REPLACED
屏幕被关闭 | Intent.ACTION_SCREEN_OFF
屏幕被打开 | Intent.ACTION_SCREEN_ON
屏幕锁屏 | Intent.ACTION_CLOSE_SYSTEM_DIALOGS
系统启动完成 | Intent.ACTION_BOOT_COMPLETED
关闭系统 | Intent.ACTION_SHUTDOWN
重启设备 | Intent.ACTION_REBOOT

注：当使用系统广播时，只需要在注册广播接收者时定义相关的action即可，并不需要手动发送广播，当系统有相关操作时会自动进行系统广播。

**c. 有序广播（Ordered Broadcast）**

- 定义

----发送出去的广播被广播接收者按照先后顺序接收
（有序是针对广播接收者而言的）

- 广播接受者接收广播的顺序规则

----同时面向静态和动态注册的广播接受者

- [ ] 按照Priority属性值从大-小排序；
- [ ] Priority属性相同者，动态注册的广播优先；

- 特点

- [ ] 接收广播按顺序接收
- [ ] 先接收的广播接收者可以对广播进行截断，即后接收的广播接收者不再接收到此广播；
- [ ] 先接收的广播接收者可以对广播进行修改，那么后接收的广播接收者将接收到被修改后的广播

- 具体使用

----有序广播的使用过程与普通广播非常类似，差异仅在于广播的发送方式：

```
sendOrderedBroadcast(intent);
```

**d. App应用内广播（Local Broadcast）**

- 背景

----Android中的广播可以跨App直接通信（exported对于有intent-filter情况下默认值为true）。

- 冲突

----可能出现的问题：
- [ ] 其他App针对性发出与当前App intent-filter相匹配的广播，由此导致当前App不断接收广播并处理；
- [ ] 其他App注册与当前App一致的intent-filter用于接收广播，获取广播具体信息；即会出现安全性 & 效率性的问题。

- 解决方案

----使用App应用内广播（Local Broadcast）

- [ ] 注册广播时将exported属性设置为false，使得非本App内部发出的此广播不被接收；
- [ ] 在广播发送和接收时，增设相应权限permission，用于权限验证；
- [ ] 发送广播时指定该广播接收器所在的包名，此广播将只会发送到此包中的App内与之相匹配的有效广播接收器中。

```
// 通过intent.setPackage(packageName)指定报名
intent.setPackage(packageName)
```
