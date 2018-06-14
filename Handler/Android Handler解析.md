### 『Android 中不允许在子线程中更新 UI』

### 1. 为什么要有Handler
----在Android中，默认所有的代码都执行在主线程。但是，在Android中有一些耗时操作，例如从网络加载一张大图片。因为要建立连接，请求服务端，解析数据等等的操作。
如果该操作发生在主线程，由于线程同一时间只能执行一个操作，所以在请求网络图片的过程中，主线程会处于阻塞状态。从而导致界面被卡主了。

----Android有一个规定，在主线程完成一个操作不能超过5秒，否则Android系统就会给用户弹出一个ANR的崩溃Dialog。（BroadcastReceiver不超过10秒）

### 2. Handler的使用
----由于主线程不能做耗时操作，所以可以在主线程中创建一个子线程，把耗时的操作放在子线程完成，这样就可以避开Android系统的ANR规则。
    如下所示，在主线程new一个子线程：

```
private void executeTask() {

    new Thread(new Runnable() {

          @Override
          public void run() {
              try {
                  Bitmap bitmap = loadImg("http://blog.happyhls.me/wp-content/uploads/2015/12/fresco-og-image-1024x362.png");
              } catch (IOException e) {
                  e.printStackTrace();
                  mHandler.sendMessage(mHandler.obtainMessage(-1,e.getMessage()));
              }
          }
      }).start();
}
```

上述代码中的loadImg()就是一个耗时操作。大概实现流程：

- a. 发送网络请求；
- b. 获取对应的图片数据；
- c. 把数据解析成Bitmap。

----拿到从服务端解析到的bitmap后，通过ImageView的setImageBitmap()方法设置到ImageView上，这样就可以看到图片了。此时，当调用setImageBitmap()后，程序又会崩溃掉。

奔溃提示如下所示：
   
```
Only the original thread that created a view hierarchy can touch its views. 
只有创建了这个 view 层次树的线程才可以去 touch(泛指操作)这个 View
```

因为setImageBitmap()方法调用发生在子线程，同时，该方法属于更新界面UI的操作，而Android系统不允许在子线程中更新UI，更新UI的操作只能发生在主线程。

在主线程中操作UI，首先要在主线程定义好handler。定义好之后，只要在子线程调用handler的sendMessage(msg, obj)方法，此时就可以把操作UI的流程跳转到主线程。

```
private void executeTask() {

    new Thread(new Runnable() {

        @Override
        public void run() {
            // 子线程

            //............. 耗时操作 ................... //

            Bitmap bitmap = loadImg("http://i.imgur.com/DvpvklR.png");

            //............. 耗时操作 ................... //

            Message msg = new Message();
            msg.what = 1;
            msg.obj = bitmap;
            mHandler.sendMessage(msg);
        }
    }).start();
}
```
从上面的代码可以看到，在子线程中，执行完耗时操作，得到bitmap后，new一个msg对象，然后把msg对象通过mHangdler的sendMessage()方法跳转到主线程，主线程中的mHandler的handMessage()方法会处理传递过来的msg对象。如下所示：

```
private Handler mHandler = new Handler() {

    @Override
    public void handleMessage(Message msg) {

        super.handleMessage(msg);
        switch (msg.what) {
            case 1:
                Bitmap bitmap = (Bitmap) msg.obj;
                imageView.setImageBitmap(bitmap);
                break;
        }
    }
};
```

### 3. Handler、Looper、MessageQueue 及 Message的关系

**a. 定义**

- Handler：用于发送消息和处理消息；
- Looper：消息轮询器，包含一个loop()方法，用于启动一个无限循环，不停地去轮询消息池；
- MessageQueue：消息池；
- Message：消息对象。

**b. 如何将它们关联起来**

一切从Handler的构造方法开始，代码如下：

```
final MessageQueue mQueue;
final Looper mLooper;
final Callback mCallback;
final boolean mAsynchronous;

public Handler(Callback callback, boolean async) {
    mLooper = Looper.myLooper();
    if (mLooper == null) {
        throw new RuntimeException(
            "Can't create handler inside thread that has not called Looper.prepare()");
    }
    mQueue = mLooper.mQueue;
    mCallback = callback;
    mAsynchronous = async;
}
```
可以看到，Handler定义了一个MessageQueue对象mQueue和一个Looper对象mLooper。
不过，Handler的这两个成员变量的初始化是通过Looper来赋值的。

```
mLooper = Looper.myLooper();
mQueue = mLooper.mQueue;

```
此时，我们将创建的Handler与Looper、MessageQueue关联起来了。而且是一对一的关系，一个Handler对应一个Looper，同时对应一个MessageQueue对象。

还可以看出，looper将mQueue对象赋值给Handler的成员变量mQueue，所以Looper和Handler持有同一个MessageQueue。

这里可以看出Looper的重要性：Handler中的Looper实例和MessageQueue实例都是通过Looper来完成赋值的。下面具体看看Looper是如何实例化的，以及Looper的mQueue是怎么来的。

从Handler的构造方法中可以看到，Handler的mLooper是这样被赋值的：

```
 mLooper = Looper.myLooper();
```
接着看myLooper()的实现：

```
static final ThreadLocal<Looper> sThreadLocal = new ThreadLocal<Looper>();

public static @Nullable Looper myLooper() {
    return sThreadLocal.get();
}
```
这里是通过ThreadLocal类的get()方法获得。很奇怪，之前我们没有在任何地方对sThreadLocal执行过set()操作。这里却直接执行get()，返回的结果必然为空啊？

但是如果返回结果为空，在new Handler()时，程序就已经挂掉了啊，因为在Handler的构造方法中，如果Looper.myLooper()返回为空，则执行如下代码：

```
mLooper = Looper.myLooper();
if (mLooper == null) {
   throw new RuntimeException(
       "Can't create handler inside thread that has not called Looper.prepare()");
}
```
但是，我们的程序没有挂掉，这意味着，在执行myLooper()方法时，返回的结果不为空。

why？我们看看在Looper中，哪里有对应的set()方法不，如下所示，找到了一个全局静态方法prepare()
```
public static void prepare() {
    prepare(true);
}

private static void prepare(boolean quitAllowed) {
    if (sThreadLocal.get() != null) {
        throw new RuntimeException("
        	Only one Looper may be created per thread");
    }
    sThreadLocal.set(new Looper(quitAllowed));
}
```
看到了没？在最后一行代码里，执行了对应的set()操作，把一个new出来的Looper直接set到sThreadLocal中。

但是到底什么时候，是谁调用了prepare()方法，从而给sThreadLocal设置了一个Looper对象呢？

Android应用在启动时，会执行到ActivityThrea类的main()方法，ActivityThrea的main()方法是一个应用启动的入口。

在这个入口里，会做很多初始化的操作。其中就有Looper相关的设置，代码如下：
```
public static void main(String[] args) {
    //............. 无关代码...............
    Looper.prepareMainLooper();
    Looper.loop();
    throw new RuntimeException("
    	Main thread loop unexpectedly exited");
}
```
从上面代码，清楚的看到，程序启动时，首先执行Looper.prepareMainLooper()方法，接着执行了loop()方法。

先来看看Looper.prepareMainLooper()方法：
```
public static void prepareMainLooper() {
    prepare(false);
    synchronized (Looper.class) {
        if (sMainLooper != null) {
            throw new IllegalStateException("
            	The main Looper has already been prepared.");
        }
        sMainLooper = myLooper();
    }
}

....

public static @Nullable Looper myLooper() {
    return sThreadLocal.get();
}

....

private static void prepare(boolean quitAllowed) {
  if (sThreadLocal.get() != null) {
      throw new RuntimeException("
      Only one Looper may be created per thread");
  }
  sThreadLocal.set(new Looper(quitAllowed));
}
```
这里，首先调用了prepare()方法，执行完成后，sThreadLocal成功绑定了一个new Looper()对象，然后执行
```
sMainLooper = myLooper();
```
可以看看sMainLooper的定义，以及myLooper()方法：
```
// guarded by Looper.class    
private static Looper sMainLooper;  

public static @Nullable Looper myLooper() {
    return sThreadLocal.get();
}
```
现在的sMainLooper就有值了，也就是说，只要我们的app启动，sMainLooper中就已经设置了一个Looper对象。以后调用sMainLooper的get()方法将返回在程序启动时设置的Looper，不会为空的。

下面再看一下main()方法中调用的Looper.loop()方法：
```
public static void loop() {

    // 获得一个 Looper 对象
    final Looper me = myLooper();

    // 拿到 looper 对应的 mQueue 对象
    final MessageQueue queue = me.mQueue;

    // 死循环监听(如果没有消息变化，他不会工作的) 不断轮训 queue 中的 Message
    for (;;) {
        // 通过 queue 的 next 方法拿到一个 Message
        Message msg = queue.next(); // might block
        // 空判断
        if (msg == null) return;
        // 消息分发   
        msg.target.dispatchMessage(msg);
        // 回收操作  
        msg.recycleUnchecked();
    }
}
```
现在，想一个简单的过程，创建一个app，什么也不做，就是一个Hello World的Android app，此时，启动程序，即使什么也不干，按照上面的代码，应该知道的是：程序中已经有一个Looper存在，并且还启动了消息轮询Looper.loop()。

但是，目前来看，他们好像没什么用，只是存在而已。此时，如果程序中使用了Handler，在主线程new Handler时，执行构造方法：
```
public Handler(Callback callback, boolean async) {
    mLooper = Looper.myLooper();
    if (mLooper == null) {
        throw new RuntimeException(
            "Can't create handler inside thread that has not called Looper.prepare()");
    }
    mQueue = mLooper.mQueue;
    mCallback = callback;
    mAsynchronous = async;
}
```
此时的myLooper()方法返回的Looper就是app启动时的那个Looper对象。我们从Looper的构造方法可知，在new Looper时，会新建一个对应的消息池对象MessageQueue
```
private Looper(boolean quitAllowed) {
    mQueue = new MessageQueue(quitAllowed);
    mThread = Thread.currentThread();
}
```
同样可以知道，Handler构造方法中的mQueue也是app启动时就已经创建好。

现在来回顾一下Handler的构造方法，在构造方法中，Handler为mQueue和mLooper分别赋值，而这两个值其实在app启动时就已经初始化好了。并且，此时已经启动了一个消息轮询，在监听mQueue中是否有新的Message。

好啦，现在这个轮询器已经好了，接下来看看发送消息的过程。
### SendMessage

使用Handler发送消息：
```
mHandler.sendMessage(msg);
```
Handler有好多相关的发送消息的方法，但是追踪源码，发现最终都来到了Handler的这个方法sendMessageAtTime()

```
public boolean sendMessageAtTime(Message msg, long uptimeMillis) {
    MessageQueue queue = mQueue;
    if (queue == null) {
        RuntimeException e = new RuntimeException(
                this + " sendMessageAtTime() called with no mQueue");
        Log.w("Looper", e.getMessage(), e);
        return false;
    }
    return enqueueMessage(queue, msg, uptimeMillis);
}
```
接着看enqueueMessage()方法

```
private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
    msg.target = this;
    // 使用默认的 handler 构造方法时，mAsynchronous 为 false。
    if (mAsynchronous) {
        msg.setAsynchronous(true);
    }
    return queue.enqueueMessage(msg, uptimeMillis);
}
```
这里有一句至关重要的代码：

```
msg.target = this;
```
我们看看Message的target是怎么声明的

```
Handler target;
```
意思就是：每个Message都有一个类型为Handler的target对象，在handler发送消息的时候，最终执行到上面的方法enqueueMessage()方法时，会自动把当前执行sendMessage()的handler对象，赋值给Message的target。

也就是说，Handler发送了Message，并且这个Message的target就是这个Handler。

想一想为什么要这么做呢？这里再说一下，Handler的作用：
- 发送消息
- 处理消息

先不看代码，我们可以想想，handler发送了message，最终这个message会被发送到MessageQueue这个消息队列里。那么最终，谁会去处理这个消息呢？

消息发送和处理遵循『谁发送，谁处理』的原则。

问题来了，按照上面说的『谁发送，谁处理』原则，那现在应该是handler自己处理了，但是在哪里处理了呢？

别急，接下来继续看消息的传递。

现在，只要发送了一条消息，消息池mQueue就会增加一条消息，Looper就会开始工作。之前已经知道：在app启动时，已经启动了Looper的loop()方法，这个方法会不断的轮询mQueue消息池，只要有消息，它就会取出消息，并处理。那到底是怎么处理的呢？再次看一下loop()的代码：

```
public static void loop() {

    // 获得一个 Looper 对象
    final Looper me = myLooper();

    // 拿到 looper 对应的 mQueue 对象
    final MessageQueue queue = me.mQueue;

    // 死循环监听(如果没有消息变化，他不会工作的) 不断轮训 queue 中的 Message
    for (;;) {
        // 通过 queue 的 next 方法拿到一个 Message
        Message msg = queue.next(); // might block
        // 空判断
        if (msg == null) return;
        // 消息分发   
        msg.target.dispatchMessage(msg);
        // 回收操作  
        msg.recycleUnchecked();
    }
}
```
看for()循环，在拿到消息后，发现msg不为空，接着就会执行下面这行非常重要的代码：

```
msg.target.dispatchMessage(msg);
```
这里执行了msg.target的dispatchMessage()方法。上面已经在sendMessage时知道：在发送消息时，会把msg的target设置为handler本身。也就是说，handler发送了消息，最终自己处理了自己刚刚分发的消息。那么Handler到底是怎么处理消息的呢？看看Handler的dispatchMessage()是怎么实现的：

```
public void dispatchMessage(Message msg) {
    if (msg.callback != null) {
        handleCallback(msg);
    } else {
        if (mCallback != null) {
            if (mCallback.handleMessage(msg)) {
                return;
            }
        }
        handleMessage(msg);
    }
}
```
可以看到，我们在发送消息时，没有给msg设置callback，也没有给handler设置mCallback，所以此时会执行handleMessage()方法：

```
public void handleMessage(Message msg) {

}
```
查看handleMessage()方法，发现这是一个空方法，所以在new Handler时，需要重写这个方法，我们就可以接受到从子线程中发送过来的消息了。

再看一遍定义Handler时，如何定义的，如何重写handleMessage()

```
private Handler mHandler = new Handler() {

    @Override
    public void handleMessage(Message msg) {

        super.handleMessage(msg);
        switch (msg.what) {
            case 1:
                Bitmap bitmap = (Bitmap) msg.obj;
                imageView.setImageBitmap(bitmap);
                break;
        }
    }
};
```
到这里，我们处理了handler发送的消息，到此Handler的内部机制大体就分析完了。但是从dispatchMessage()方法，可以看出，Handler在处理消息时的顺序是什么呢？

```
public void dispatchMessage(Message msg) {
    if (msg.callback != null) {
        handleCallback(msg);
    } else {
        if (mCallback != null) {
            if (mCallback.handleMessage(msg)) {
                return;
            }
        }
        handleMessage(msg);
    }
}
```
- 首先，判断Message对象的callback是否为空，如果不为空，则直接调用handleCallback()方法，并把msg对象传递进去。这样消息就被处理了。我们看看Message的handleCallback()方法：

```
private static void handleCallback(Message message) {
    message.callback.run();
}
```
直接调用Handler.post的Runnable对象的run()方法。

- 然后，如果在发送消息时，没有给Message设置callback对象，则程序会执行到else语句块。此时，先判断Handler的mCallback对象是否为空，如果不为空，则直接调用mCallback的handleMessage()方法。
- 最后，只有当Handler的mCallback对象为空时，才会执行handleMessage()方法。

以上就是消息处理的整个过程。

那么在处理消息时，什么时候才能执行到第一种情况呢？也就是通过Message的callback对象处理。查看源码发现：

```
Runnable callback;
```
callback是一个Runnable接口，那如何设置Message的callback参数呢？Handler发送消息时，除了使用sendMessage()方法，还可以使用post方法，而它的参数正好就是Runnable。

```
public final boolean post(Runnable r) {
   return  sendMessageDelayed(getPostMessage(r), 0);
}
```
接着看看getPostMessage()这个方法：

```
private static Message getPostMessage(Runnable r) {
    Message m = Message.obtain();
    m.callback = r;
    return m;
}
```
代码看到这里，已经很清楚了，getPostMessage()返回了一个Message对象，这个对象设置了刚才传递过来的runnable对象。

Handler post用法：
```
new Handler().post(new Runnable() {

    @Override
    public void run() {
    
        //更新UI
    }
});
```
在run方法中，可以写更新UI的代码。其实这个Runnable并没有创建什么线程，而是发送了一条消息。因为调用的是run方法，而不是start方法。

一般该用法在子线程中使用。

```
new Thread(new Runnable() {

    @Override
    public void run() {
        try {
            Thread.sleep(1000);

            new Handler().post(new Runnable() {

                @Override
                public void run() {
                	
                	//更新UI
                }
            });

            // 延时
            new Handler().postDelayed(new Runnable() {

                @Override
                public void run() {

                    //更新UI
                }
            }, 1000);

        } catch (InterruptedException e) {
            e.printStackTrace();
		}
    }
}).start();
```
最后看两张图：
![image](https://github.com/zhaoqingyue/Notes/blob/master/android/handler1.jpg)
![image](https://github.com/zhaoqingyue/Notes/blob/master/android/handler2.png)





























