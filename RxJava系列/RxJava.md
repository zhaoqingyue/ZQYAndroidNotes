### RxJava

开源项目
- https://github.com/ReactiveX/RxJava
- https://github.com/ReactiveX/RxAndroid

导入依赖
```
compile 'io.reactivex:rxjava:1.0.14'
compile 'io.reactivex:rxandroid:1.0.1'
```

**什么是Rx？**

---- Reactive Extensions的缩写。Rx是微软.NET的一个响应式扩展。Rx借助可观察的序列提供一种简单的方式来创建异步的，基于事件驱动的程序。

---- RxJava是Reactive Extensions的Java VM实现：用于通过使用可观察序列来编译异步和基于事件的程序的库。归根到底，就是异步。


**为什么要用RxJava？**

---- 用简洁的流式操作处理各类复杂的异步请求。


**关键字**
- 异步
- 链式调用
- 观察者模式

**基本实现**

![image](https://github.com/zhaoqingyue/ZQYRxJavaDemo/blob/master/img/1.jpg)

基本对象：
- Observable ---- 被观察者，发射源
- Observer ---- 观察者，接收源
- Subscriber ---- 订阅者，也是接收源
- Subscribe ---- 订阅
- Subscription ---- Observable调用subscribe( )方法返回的对象
- onEvent ---- 事件
- Action0 ---- RxJava中的一个接口，它只有一个无参call（）方法，且无返回值，同样还有Action1，Action2...Action9等，Action1封装了含有1 个参的call（）方法，即call（T t），Action2封装了含有 2 个参数的call方法，即call（T1 t1，T2 t2），以此类推；
- Func0 ---- 与Action0非常相似，也有call（）方法，但是它是有返回值的，同样也有Func0、Func1...Func9;


基本用法：
1. 创建被观察者(Observable)
2. 创建观察者Observer或Subscriber
3. 订阅事件subscribe

### 1. 创建被观察者(Observable) ---- 发射数据
```
Observable<String> observable = Observable.create(new Observable.OnSubscribe<String>() {

    @Override
    public void call(Subscriber<? super String> sub) {
        sub.onNext("1. 创建Observable\n");
        sub.onNext("2. 创建Subscriber\n");
        sub.onNext("3. 关联Observable与Subscriber");
        sub.onCompleted();
    }
});
```
常用到的几种方法：

- creat()：使用一个函数从头创建一个Observable

```
Observable.create(new Observable.OnSubscribe<String>() {

    @Override
    public void call(Subscriber<? super String> subscriber) {
        subscriber.onNext("One");
        subscriber.onNext("TWO");
        subscriber.onNext("THREE");
        subscriber.onCompleted();
    }
});
```

- just(T…)：将一个或多个对象转换成发射这个或这些对象的一个Observable

```
Observable.just("One", "TWO", "THREE");
// 将会依次调用：
// onNext("One");
// onNext("TWO");
// onNext("THREE");
// onCompleted();
```

- from(T[])：将一个Iterable,一个Future, 或者一个数组转换成一个Observable

```
String[] test = {"One", "TWO", "THREE"};
Observable.from(test);
// 将会依次调用：
// onNext("One");
// onNext("TWO");
// onNext("THREE");
// onCompleted();
```

- repeat()：创建一个重复发射指定数据或数据序列的Observable

```
Observable.just("One", "TWO", "THREE")
    .repeat(3)
    .subscribe(new Action1<String>() {
    
        @Override
        public void call(String s) {
                    
        }
});
```

- defer()：只有当订阅者订阅才创建Observable；为每个订阅创建一个新的 Observable

```
Observable.defer(new Func0<Observable<String>>() {

    @Override
    public Observable<String> call() {
        return Observable.just("One");
    }
});
```

- range()：创建一个发射指定范围的整数序列的Observable

```
// 依次发射 1, 2, 3, 4, 5
Observable.range(1, 5)
```

- interval()：创建一个按照给定的时间间隔发射整数序列的Observable

```
// 每隔 1 s 发送一个序列号，从 0 开始，每次累加 1。
Observable.interval(1, TimeUnit.SECONDS);
```

- timer()：创建一个在给定的延时之后发射单个数据的Observable

```
// 定时 1 s
Observable.timer(1, TimeUnit.SECONDS);
```


### 2. 创建观察者Observer或Subscriber ---- 接收数据
----Subscriber继承Observer： 定义三个回调方法

```
Observer<String> observer = new Observer<String>() {

    @Override
    public void onNext(String s) {
    }

    @Override
    public void onCompleted() {
    }

    @Override
    public void onError(Throwable e) {
    }
};
```

- onNext(T item)

---- Observable调用这个方法发射数据，方法的参数就是Observable发射的数据，该方法可能会被调用多次。

- onComplete()

---- 正常终止，如果没有遇到错误，Observable在最后一次调用onNext之后调用此方法。

- onError(Exception ex)

---- 当Observable遇到错误或者无法返回期望的数据时会调用这个方法，这个调用会终止 Observable，后续不会再调用onNext和onCompleted，onError方法的参数是抛出的异常。

根据Observable协议的定义，onNext可能会被调用零次或者很多次，最后会有一次 onCompleted或onError调用（不会同时），传递数据给onNext通常被称作发射， onCompleted和onError被称作通知。

**Observer 和 Subscriber 的区别主要有两点：**

- onStart(): 这是 Subscriber 增加的方法。它会在 subscribe 刚开始，而事件还未发送之前被调用，可以用于做一些准备工作，例如数据的清零或重置。这是一个可选方法，默认情况下它的实现为空。需要注意的是，如果对准备工作的线程有要求（例如弹出一个显示进度的对话框，这必须在主线程执行），onStart() 就不适用了，因为它总是在 subscribe 所发生的线程被调用，而不能指定线程。要在指定的线程来做准备工作，可以使用 doOnSubscribe() 方法。

- unsubscribe(): 这是 Subscriber 所实现的另一个接口 Subscription 的方法，用于取消订阅。在这个方法被调用后，Subscriber 将不再接收事件。一般在这个方法调用前，可以使用 isUnsubscribed() 先判断一下状态。 unsubscribe() 这个方法很重要，因为在 subscribe() 之后， Observable 会持有 Subscriber 的引用，这个引用如果不能及时被释放，将有内存泄露的风险。所以最好保持一个原则：要在不再使用的时候尽快在合适的地方（例如 onPause() onStop() 等方法中）调用 unsubscribe() 来解除引用关系，以避免内存泄露的发生。


### 3. 订阅事件subscribe ---- 关联Observable与Observer（或Subscriber）
```
/**
 * 一旦observer订阅了observable，
 * observable就调用observer对象的onNext和onComplete方法
 */
observable.subscribe(observer) ;
```

### 线程调度器Scheduler

RxJava中可用的调度器种类:

调度器类型 | 效果
---|---
Schedulers.computation() | 用于计算任务，如事件循环或回调处理，不要用IO操作（IO操作请使用Scheduler.io()）; 默认线程数等于处理器的数量
Schedulers.from(executor) | 使用指定的Executor作为调度器
Schedulers.immediate() | 在当前线程立即开始执行任务
Schedulers.io() | 用于IO密集型任务，如异步阻塞IO操作；默认是一个CachedThreadScheduler，像一个有线程缓存的新线程调度器
Schedulers.newThread() | 为每一个任务创建一个新的线程
Schedulers.trampoline() | 当其它排队的任务完成后，在当前线程排队开始执行


RxAndroid中添加了专用的AndroidSchedulers.mainThread()，它指定的操作将在 Android 主线程运行，也就是我们的UI线程。

**使用SubScribeOn和ObserverOn操作符**

- SubscribeOn(): 订阅事件发生的线程，事件产生的线程，只允许执行一次
- ObserveOn：指定回调发生的线程，事件消费的线程，可以执行多次

```
.subscribeOn(Schedulers.io())
.observeOn(AndroidSchedulers.mainThread())
```
subscribeOn原理图：

![image](https://github.com/zhaoqingyue/ZQYRxJavaDemo/blob/master/img/subscribe.jpg)

observeOn原理图：

![image](https://github.com/zhaoqingyue/ZQYRxJavaDemo/blob/master/img/observe.jpg)

**基本用法：**
```
Observable.just("One", "Two", "Three")
    .subscribeOn(Schedulers.io()) // 指定 subscribe() 发生在 IO 线程,事件产生在Io线程
    .observeOn(AndroidSchedulers.mainThread()) // 指定 Subscriber 的回调发生在主线程
    .subscribe(new Action1<String>() {
    
        @Override
        public void call(String s) {
            Log.i("RxJavaTest",  s);
        }
    });
```

**多次切换线程**

```
Observable.just("One"，"Two ","Three") // 发生在IO 线程，由 subscribeOn() 指定，subscribeOn()只允许执行一次，你也可以指定多个，但是只有第一个起作用
    .subscribeOn(Schedulers.io())
    .observeOn(Schedulers.newThread())
    .map() // 发生在新线程，由 observeOn() 指定
    .observeOn(Schedulers.io())
    .map() // 发生在IO 线程，由 observeOn() 指定
    .observeOn(AndroidSchedulers.mainThread)
    .subscribe(subscriber);  // 发生在UI主线程，由 observeOn() 指定
```

使用示例：

1. 从数据库的用户表查找出所有用户数据

```
Observable.create(new Observable.OnSubscribe<List<User>>() {

    @Override
    public void call(Subscriber<? super List<User>> subscriber) {
        List<User> userList = null;
        ···
        //从数据库获取用户表数据并赋给userList
        ···
        subscriber.onNext(userList);
    }
}).subscribe(new Action1<List<User>>() { 

    @Override
    public void call(List<User> users) {
        //获取到用户信息列表
    }
});
```

2. 不想要所有用户了，只要名字叫“小明”的用户（假设名字唯一）

```
Observable.create(new Observable.OnSubscribe<List<User>>() {

    @Override
    public void call(Subscriber<? super List<User>> subscriber) {
        List<User> userList = null;
        ···
        //从数据库获取用户表数据并赋给userList
        ···
        subscriber.onNext(userList);
    }
}).flatMap(new Func1<List<User>, Observable<User>>() {

    @Override
    public Observable<User> call(List<User> users) {
        return Observable.from(users);
    }
}).filter(new Func1<User, Boolean>() {

    @Override
    Boolean call(User user) {
        return user.getName().equals("小明");
    }
}).subscribe(new Action1<User>() {

    @Override
    public void call(User user) {
        //拿到谜之小明的数据
    }
});
```

3. 不要小明了，要小明的爸爸的数据

```
Observable.create(new Observable.OnSubscribe<List<User>>() {

    @Override
    public void call(Subscriber<? super List<User>> subscriber) {
        List<User> userList = null;
        ···
        //从数据库获取用户表数据并赋给userList
        ···
        subscriber.onNext(userList);
    }
}).flatMap(new Func1<List<User>, Observable<User>>() {

    @Override
    public Observable<User> call(List<User> users) {
        return Observable.from(users);
    }
}).filter(new Func1<User, Boolean>() {

    @Override
    Boolean call(User user) {
        return user.getName().equals("小明");
    }
}).map(new Func1<User, User>() {

    @Override
    public User call(User user) {
        //根据小明的数据user从数据库查找出小明的父亲user2
        return user2;
    }
}).subscribe(new Action1<User>() {

    @Override
    public void call(User user) {
        //拿到谜之小明的数据
    }
});
```

### 延伸：doOnSubscribe()
---- doOnSubscribe() 一般用于执行一些初始化操作。

Observable.doOnSubscribe() 和 Subscriber.onStart() 同样是在 subscribe() 调用后而且在事件发送前执行，但区别在于它可以指定线程。默认情况下， doOnSubscribe() 执行在 subscribe() 发生的线程；而如果在 doOnSubscribe() 之后有 subscribeOn() 的话，它将执行在离它最近的 subscribeOn() 所指定的线程。

```
Observable.create(new Observable.OnSubscribe<String>() {

    @Override
    public void call(Subscriber<? super String> sub) {
        sub.onNext("Hello");
        sub.onCompleted();
    }
    
}).subscribeOn(Schedulers.io())
    .doOnSubscribe(new Action0() {
    
        @Override
        public void call() {
            // 需要在主线程执行
            progressBar.setVisibility(View.VISIBLE); 
        }
    })
    .subscribeOn(AndroidSchedulers.mainThread()) // 指定主线程
    .observeOn(AndroidSchedulers.mainThread())
    .subscribe(subscriber);
```
