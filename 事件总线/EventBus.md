### EventBus

----EventBus是一个发布/订阅（publish/subscribe）事件总线。

**功能**

----替代Intent、Handler、BroadcastReceiver在Fragment、Activity、Service之间传递消息。

**元素**
- Event：事件
- Subscriber：事件订阅者，接收特定的事件
- Publisher：事件发布者，通知 Subscriber 有事件发生

**1. 使用EventBus3.0流程**

**1.1 添加依赖**

```
compile 'org.greenrobot:eventbus:3.0.0'
```

**1.2 定义事件**

```
public class MessageEvent {

    public final String message;

    public MessageEvent(String message) {
        this.message = message;
    }
}
```

**1.3 准备订阅者**

```
// This method will be called when a MessageEvent is posted 
@Subscribe 
public void onMessageEvent(MessageEvent event){ 
    Toast.makeText(getActivity(), event.message, Toast.LENGTH_SHORT).show(); 
} 

// This method will be called when a SomeOtherEvent is posted 
@Subscribe 
public void handleSomethingElse(SomeOtherEvent event){     
    doSomethingWith(event); 
}


@Override
public void onStart() {
    super.onStart();
    // 注册事件接收者
    EventBus.getDefault().register(this);
}

@Override
public void onStop() {
    // 注销事件接收
    EventBus.getDefault().unregister(this);
    super.onStop();
}
```

**1.4 发送事件**

```
EventBus.getDefault().post(new MessageEvent("Hello everyone!"));
```

**2. ThreadMode线程通信**

----EventBus可以很简单的实现线程间的切换，包括后台线程、UI线程、异步线程。

**2.1 ThreadMode.POSTING**

```
// 默认调用方式，在调用post方法的线程执行，避免了线程切换，性能开销最少
// Called in the same thread (default) 
@Subscribe(threadMode = ThreadMode.POSTING) 
// ThreadMode is optional here 
public void onMessage(MessageEvent event) { 
    log(event.message); 
}
```

**2.2 ThreadMode.MAIN**

```
// Called in Android UI's main thread 
@Subscribe(threadMode = ThreadMode.MAIN) 
public void onMessage(MessageEvent event) {
    textField.setText(event.message); 
}
```

**2.3 ThreadMode.BACKGROUND**

```
// 如果调用post方法的线程不是主线程，则直接在该线程执行 
// 如果是主线程，则切换到后台单例线程，多个方法公用同个后台线程，按顺序执行，避免耗时操作 
// Called in the background thread 
@Subscribe(threadMode = ThreadMode.BACKGROUND) 
public void onMessage(MessageEvent event) { 
    saveToDisk(event.message); 
}
```

**2.4 ThreadMode.ASYNC**

```
//开辟新独立线程，用来执行耗时操作，例如网络访问
//EventBus内部使用了线程池，但是要尽量避免大量长时间运行的异步线程，限制并发线程数量 
//可以通过EventBusBuilder修改，默认使用Executors.newCachedThreadPool() 
// Called in a separate thread 
@Subscribe(threadMode = ThreadMode.ASYNC) 
public void onMessage(MessageEvent event) { 
    backend.send(event.message);
}
```

**3. proguard 混淆处理**

```
#EventBus
 -keepclassmembers class ** {
    public void onEvent*(**);
    void onEvent*(**);
 }
```
