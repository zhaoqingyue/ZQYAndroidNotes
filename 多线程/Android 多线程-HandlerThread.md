### Android 多线程-HandlerThread

----封装好的轻量级、异步类

**1. 作用**
- 实现多线程（在工作线程中执行任务，如：耗时任务）
- 异步通信、消息传递
    - 实现工作线程 & 主线程（UI线程）之间的通信，即：将工作线程的执行结果传递给主线程，从而在主线程中执行相关的UI操作，从而保证线程安全。

**优点：**
- 方便实现异步通信，不需使用 "任务线程（如继承Thread类） + Handler"的组合

**2. 原理**

----Thread类 + Handler类机制

**3. 使用步骤**
- 创建HandlerThread实例对象
- 启动线程
- 创建工作线程Handler & 复写handleMessage()
- 使用工作线程Handler向工作线程的消息队列发送消息
- 结束线程，即停止线程的消息循环

```
// 步骤1：创建HandlerThread实例对象 
// 传入参数 = 线程名字，作用 = 标记该线程 
HandlerThread mHandlerThread = new HandlerThread("handlerThread"); 

// 步骤2：启动线程 
mHandlerThread.start(); 

// 步骤3：创建工作线程Handler & 复写handleMessage（） 
// 作用：关联HandlerThread的Looper对象、实现消息处理操作 & 与其他线程进行通信 
// 注：消息处理操作（HandlerMessage（））的执行线程 = mHandlerThread所创建的工作线程中执行
Handler workHandler = new Handler(handlerThread.getLooper()) { 

    @Override 
    public boolean handleMessage(Message msg) { 
        
        //消息处理 
        return true; 
    } 
}); 

// 步骤4：使用工作线程Handler向工作线程的消息队列发送消息 
// 在工作线程中，当消息循环时取出对应消息 & 在工作线程执行相关操作 
// a. 定义要发送的消息 
Message msg = Message.obtain(); 
msg.what = 2;  // 消息的标识 
msg.obj = "B"; // 消息的存放 
// b. 通过Handler发送消息到其绑定的消息队列 
workHandler.sendMessage(msg); 

// 步骤5：结束线程，即停止线程的消息循环 
mHandlerThread.quit();
```
