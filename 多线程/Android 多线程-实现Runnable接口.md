### Android 多线程-实现Runnable接口

----抽象接口，实现多线程

**1.1 使用步骤**

- 创建线程辅助类 
    - 实现Runnable接口
- 复写run() 
    - 定义线程行为
- 创建线程辅助对象 
    - 实例化辅助类
- 创建线程对象
    - 实例化线程类
    - 线程类 = Thread类
    - 创建时，传入线程辅助类对象
- 控制线程状态 
    - 运行、睡眠、 挂起/停止

**1.2 具体使用**
```
// 步骤1：创建线程辅助类，实现Runnable接口 
class MyThread implements Runnable { 

    // 步骤2：复写run()，定义线程行为 
    @Override 
    public void run() {
    
    } 
} 

// 步骤3：创建线程辅助对象，即实例化线程辅助类 
MyThread mt = new MyThread(); 

// 步骤4：创建线程对象，即实例化线程类；线程类 = Thread类； 
// 创建时通过Thread类的构造函数传入线程辅助类对象 
// 原因：Runnable接口并没有任何对线程的支持，我们必须创建线程类（Thread类）的实例，从Thread类的一个实例内部运行 
Thread td = new Thread(mt); 

// 步骤5：通过线程对象控制线程的状态，如：运行、睡眠、挂起/停止 
// 当调用start()方法时，线程对象会自动回调线程辅助类对象的run()，从而实现线程操作 
td.start();
```

**1.3 匿名类**
```
new Thread(new Runnable() {
            
    @Override
    public void run() {
        // 定义线程行为         
    }
}).start();

new Thread(new Runnable() {

    @Override
    public void run() {
        // 定义线程行为       
    }
}, "线程名称").start();
```
