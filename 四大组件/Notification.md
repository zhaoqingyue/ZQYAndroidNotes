### Notification

----Notification：状态栏通知。

**1. Notification的组成元素**

- Icon：大图标
- Secondary Icon：小图标
- Title：标题
- Message：内容信息
- TimeStamp：通知时间（默认系统时间，也可以通过setEhen()来设置）

**2. Notification的基本使用流程**

----通知栏主要涉及2个类：Notification  和 NotificationManager。

-  Notification：通知信息类，对应通知栏的各个属性
-  NotificationManager：状态栏通知的管理类，负责发通知、清除通知等操作。

**基本使用流程：**

- a. 获得NotificationManager对象

```
NotificationManager mNManager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
```
- b. 创建一个通知栏的Builder构造类

```
Notification.Builder mBuilder = new Notification.Builder(this);
```
- c. 对Builder进行相关的设置，比如标题，内容，图标，动作等
- d. 调用Builder的build()方法为notification赋值
- e. 调用NotificationManager的notify()方法发送通知

PS：可以调用NotificationManager的cancel()方法取消通知。

3. Notification的设置方法

```
// 创建通知栏的Builder构造类
Notification.Builder mBuilder = new Notification.Builder(this);

// 然后调用下面的方法进行设置
```
- setContentTitle(CharSequence)：设置标题
- setContentText(CharSequence)：设置内容
- setSubText(CharSequence)：设置内容下面一小行的文字
- setTicker(CharSequence)：设置收到通知时在顶部显示的文字信息
- setWhen(long)：设置通知时间，一般设置的是收到通知时的System.currentTimeMillis()
- setSmallIcon(int)：设置右下角的小图标，在接收到通知的时候顶部也会显示这个小图标
- setLargeIcon(Bitmap)：设置左边的大图标
- setAutoCancel(boolean)：用户点击Notification点击面板后是否让通知取消(默认不取消)
- setDefaults(int)：向通知添加声音、闪灯和振动效果的最简单、 使用默认（defaults）属性，可以组合多个属性

```
Notification.DEFAULT_VIBRATE(添加默认震动提醒)；
Notification.DEFAULT_SOUND(添加默认声音提醒)；
Notification.DEFAULT_LIGHTS(添加默认三色灯提醒)
Notification.DEFAULT_ALL(添加默认以上3种全部提醒)
```
- setVibrate(long[])：设置振动方式
- setSound(Uri)：设置接收到通知时的铃声
- setProgress(int, int, boolean)：设置带进度条的通知

---- 参数依次为：进度条最大数值，当前进度，进度是否不确定 如果为确定的进度条：调用setProgress(max, progress, false)来设置通知， 在更新进度的时候在此发起通知更新progress，并且在下载完成后要移除进度条 ，通过调用setProgress(0, 0, false)既可。如果为不确定（持续活动）的进度条， 这是在处理进度无法准确获知时显示活动正在持续，所以调用setProgress(0, 0, true) ，操作结束时，调用setProgress(0, 0, false)并更新通知以移除指示条。

- setContentIntent(PendingIntent)：设置PendingIntent

----它可以设置执行次数，主要用于远程服务通信、闹铃、通知、启动器、短信中。

比如：通过 Pending启动Activity：getActivity(Context, int, Intent, int)，还可以启动Service或者Broadcast。

PendingIntent的位标识符(第四个参数)：

1. FLAG_ONE_SHOT： 表示返回的PendingIntent仅能执行一次，执行完后自动取消
1. FLAG_NO_CREATE：表示如果描述的PendingIntent不存在，并不创建相应的PendingIntent，而是返回NULL
1. FLAG_CANCEL_CURRENT：表示相应的PendingIntent已经存在，则取消前者，然后创建新的PendingIntent
1. FLAG_UPDATE_CURRENT： 表示更新的PendingIntent

使用示例：

```
//点击后跳转Activity
Intent intent = new Intent(context,XXX.class);  
PendingIntent pendingIntent = PendingIntent.getActivity(context, 0, intent, 0);  
mBuilder.setContentIntent(pendingIntent)  
```
- setPriority(int)：设置优先级

优先级 | 用户
---|---
PRIORITY_MAX | 重要而紧急的通知，通知用户该事件紧迫或需要立即处理
PRIORITY_HIGH | 重要的通信内容，例如：短消息或者聊天
PRIORITY_DEFAULT | 默认优先级，用于没有特殊优先级分类的通知
PRIORITY_LOW | 低优先级，可以通知用户但又不是很紧急的事件
PRIORITY_MIN | 用于后台消息，例如：天气或者位置信息

```
setPriority(Notification.PRIORITY_HIGH)；
```
