### RxBinding
---- RxBinding 是把 Android 中各种 UI 控件的事件转换为 RxJava 中的数据流。

这样就可以把 UI 控件的事件当做 RxJava 中的数据流来使用了。 

比如 View 的 onClick 事件，使用 RxView.clicks(view) 即可获取到一个 Observable 对象，每当用户点击这个 View 的时候，该 Observable 对象就发射一个事件（onNext 被调用）， Observable 的 Observer 订阅者就可以通过 onNext 回调知道用户点击了 View。