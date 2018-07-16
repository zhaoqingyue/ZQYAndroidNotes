### Android 多线程-AsyncTask

----一个封装好的轻量级、异步、抽象类（使用时实现子类）

**1. 作用**

- 实现多线程（在工作线程中执行任务，如：耗时任务）
- 异步通信、消息传递
    - 实现工作线程 & 主线程（UI线程）之间的通信，即：将工作线程的执行结果传递给主线程，从而在主线程中执行相关的UI操作，从而保证线程安全。

**优点：**
- 方便实现异步通信，不需使用 "任务线程（如继承Thread类） + Handler"的组合

**2. 类 & 方法**

**2.1 类**

----AsyncTask类属于抽象类，使用时需实现子类

```
public abstract class AsyncTask<Params, Progress, Result> { 
    ... 
} 

// 整体作用：控制AsyncTask子类执行线程任务时各个阶段的返回类型 
// a. Params：开始异步任务执行时传入的参数类型，对应excute（）中传递的参数 
// b. Progress：异步任务执行过程中，返回下载进度值的类型 
// c. Result：异步任务执行完成后，返回的结果类型，与doInBackground()的返回值类型保持一致 
// 注： 
// a. 使用时并不是所有类型都被使用 
// b. 若无被使用，可用java.lang.Void类型代替 
// c. 若有不同业务，需额外再写1个AsyncTask的子类
```

**2.2 方法**

AsyncTask 核心 & 常用的方法如下：

- execute(Params params)
    - 触发执行异步线程任务
    - 手动调用
    - 必须在UI线程（主线程）中调用
- onPreExecute()
    - 执行线程任务前的操作
    - 执行execute()前自动调用
    - 用于界面的初始化操作，如：显示进度条dialog
- doInBackground(Params params)
    - 接收输入参数
    - 执行任务中的耗时操作
    - 返回线程任务执行的结果 
    - onPreExecute()执行完后，自动调用
    - 执行过程中，可调用publishProgress()更新进度信息
- onProgressUpdate(Progress progress)
    - 在主线程显示线程任务执行的进度
    - 调用publishProgress(Progress progress)时，自动调用
- onPostExecute()
    - 接收线程任务执行结束
    - 将执行结果显示到UI组件
    - 线程任务结束时，自动调用
- onCanceled()
    - 将异步任务设置为取消状态
    - 异步任务被取消时，自动调用

**3. 使用步骤**

- 创建AsyncTask子类 & 根据需求实现核心方法
- 创建AsyncTask子类的实例对象
- 手动调用execute()执行异步线程任务

具体代码：
```
/** 
 * 步骤1：创建AsyncTask子类 
 * 注： 
 * a. 继承AsyncTask类 
 * b. 为3个泛型参数指定类型；若不使用，可用java.lang.Void类型代替 
 * c. 根据需求，在AsyncTask子类内实现核心方法 
 */ 
private class MyTask extends AsyncTask<Params, Progress, Result> { 
    
    // 方法1：onPreExecute（） 
    // 作用：执行线程任务前的操作（根据需求复写 ）
    @Override 
    protected void onPreExecute() { 
        // ...
    } 
    
    // 方法2：doInBackground（） 
    // 作用：接收输入参数、执行任务中的耗时操作、返回线程任务执行的结果 
    @Override 
    protected String doInBackground(String... params) {
        // 自定义的线程任务 
        
        // 可调用publishProgress（）显示进度, 之后将执行onProgressUpdate（） 
        publishProgress(progress); 
    } 
    
    // 方法3：onProgressUpdate（） 
    // 作用：在主线程 显示线程任务执行的进度（根据需求复写 ）
    @Override 
    protected void onProgressUpdate(Integer... progresses) { 
        // ... 
    } 
    
    // 方法4：onPostExecute（） 
    // 作用：接收线程任务执行结果、将执行结果显示到UI组件 
    @Override 
    protected void onPostExecute(String result) { 
        // UI操作
    } 
    
    // 方法5：onCancelled() 
    // 作用：将异步任务设置为取消状态 
    @Override 
    protected void onCancelled() { 
        // ... 
    } 
} 

/** 
 * 步骤2：创建AsyncTask子类的实例对象
 * 注：AsyncTask子类的实例必须在UI线程中创建 
 */ 
MyTask mTask = new MyTask(); 

/** 
 * 步骤3：手动调用execute(Params... params) 从而执行异步线程任务
 * 注： 
 * a. 必须在UI线程中调用
 * b. 同一个AsyncTask实例对象只能执行1次，若执行第2次将会抛出异常
 * c. 执行任务中，系统会自动调用AsyncTask的一系列方法：onPreExecute()、doInBackground()、onProgressUpdate()、onPostExecute() 
 * d. 不能手动调用上述方法 
 */ 
mTask.execute()；
```

常见报错：
```
// 一个AsyncTask只能执行一次；当想要再次执行时，只好再new一个任务，否则，要报异常
java.lang.IllegalStateException: Cannot execute task: the task has already been executed (a task can be executed only once)
```