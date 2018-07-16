### Android 多线程-继承Thread类

----继承Thread类 & 复写run()方法，封装所需线程操作，实现多线程

**1.1 使用步骤**
- 创建线程类 
    - 继承Thread
- 复写run() 
    - 定义线程行为
- 创建线程对象 
    - 实例化线程类
- 控制线程状态
    - 运行、睡眠、挂起/停止

**1.2 具体使用**
```
// 步骤1：创建线程类
class MyThread extends Thread { 

    // 步骤2：复写run()
    @Override 
    public void run(){ 
        // 定义线程行为 
    } 
} 

// 步骤3：创建线程对象
MyThread mt = new MyThread("线程名称"); 

// 步骤4：通过 线程对象 控制线程的状态，如：运行、睡眠、挂起/停止 
mt.start();
mt.wait();
mt.stop();
```

**1.3 匿名类**
```
// 步骤1：采用匿名类，直接创建线程类的实例 
new Thread("线程名称") { 

    // 步骤2：复写run()
    @Override 
    public void run() { 
        // 步骤3：通过线程对象，控制线程的状态，如：运行、睡眠、挂起/停止 
    }
}.start();
```
