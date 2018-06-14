### RxLifecyle

**开源项目**

https://github.com/trello/RxLifecycle

**导入依赖**

```
compile 'com.trello:rxlifecycle:0.3.1'
compile 'com.trello:rxlifecycle-components:0.3.1'
```

---- RxLifecycle为我们提供了可以直接继承的RxAppCompatActivity，RxActivity，RxFragment,RxDialogFragment等等，可以很方便的让组件生命周期和Rx命周期绑定。


由于 RxJava Observable 订阅后（调用 subscribe 函数），一般会在后台线程执行一些操作（比如访问网络请求数据），当后台操作返回后，调用 Observer 的 onNext 等函数，然后在 更新 UI 状态。 但是后台线程请求是需要时间的，如果用户点击刷新按钮请求新的信息，在刷新还没有完成的时候，用户退出了当前界面，返回到前面的界面，这个时候刷新的 Observable 如果不取消订阅，则会导致之前的 Activity 无法被 JVM 回收导致内存泄露。 

这就是 Android 里面的生命周期管理需要注意的地方，RxLifecycle 就是用来干这事的。比如下面的示例：


```
myObservable
    .compose(RxLifecycle.bindUntilEvent(lifecycle, ActivityEvent.DESTROY))
    .subscribe();
```

这样Activity在destroy的时候就会自动取消这个observer。

**两种使用方法**
- 手动设置取消订阅的生命周期

```
.compose(this.<Long>bindUntilEvent(ActivityEvent.PAUSE))
```

- 手动绑定，自动取消

```
.compose(this.<Long>bindToLifecycle())
```
