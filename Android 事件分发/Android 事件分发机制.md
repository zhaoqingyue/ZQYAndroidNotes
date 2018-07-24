### Android 事件分发机制

**1. 基础知识**

**1.1 事件分发的对象**

----点击事件（Touch事件）

- 当用户触摸屏幕时（View或ViewGroup派生的控件），将产生点击事件

> Touch事件被封装成MotionEvent对象

- 事件类型（4种）

事件类型 | 动作
---|---
MotionEvent.ACTION_DOWN | 按下View（事件的开始）
MotionEvent.ACTION_UP | 抬起View
MotionEvent.ACTION_MOVE | 滑动View
MotionEvent.ACTION_CANCEL | 结束事件

- 当一个点击事件产生后，系统需把该事件传递给一个具体的View去处理

**1.2 事件分发的本质**

----将点击事件（MotionEvent）传递到某个具体的View & 处理的整个过程

> 事件传递的过程 = 分发过程

**1.3 事件传递对象**

----Activity & ViewGroup & View

Android的UI界面由Activity、ViewGroup、View 及其派生类组成。

![image](https://note.youdao.com/favicon.ico)

对象 | 说明
---|---
Activity | 控制生命周期 & 处理事件
ViewGroup | View集合
View | UI组件的基类

**1.4 事件分发的顺序**

----Activity -> ViewGroup -> View

> 点击事件发生后，事件先传到Activity，然后传到ViewGroup，最终传到 View

**1.5 事件分发过程的方法**

- dispatchTouchEvent() 
- onTouchEvent()
- onInterceptTouchEvent()


方法 | 作用 | 调用时刻
---|---|---
dispatchTouchEvent()  | 分发点击事件 | 点击事件传递给当前View时调用
onTouchEvent() | 处理点击事件 | 在dispatchTouchEvent()内部调用
onInterceptTouchEvent() | 判断是否拦截了某个事件 | 在ViewGroup的dispatchTouchEvent()内部调用

**2. 事件分发机制**

----Android事件分发流程：Activity -> ViewGroup -> View 

- Activity事件分发机制
- ViewGroup事件分发机制
- View事件分发机制


**3. 事件分发方法**

**3.1 dispatchTouchEvent()**

- 作用：分发（传递）点击事件
- 作用对象：Activity & ViewGroup & View
- 调用时刻：当点击事件传递到当前层的时（Activity & ViewGroup & View）
- 返回结果
    - 默认：直接调用以下方法
        - Activity：调用ViewGroup.dispatchTouchEvent() / Activity.onTouchEvent()
        - ViewGroup：调用自身的onInterceptTouchEvent()
        - View：调用自身的onTouchEvent()
    - true：当前事件被消费（事件被View / ViewGroup接收 & 处理）
        - 事件停止分发，逐层向上返回
    - false：当前事件没有被消费
        - 将事件回传给上层的onTouchEvent()处理

**3.2 onTouchEvent()**

- 作用：处理点击事件
- 作用对象：Activity & ViewGroup & View
- 调用时刻：在dispatchTouchEvent()内部调用
- 返回结果
    - true：当前使用对象（Activity & ViewGroup & View）处理了当前事件
        - 事件停止分发，逐层向上返回
    - false：当前使用对象（Activity & ViewGroup & View）没有处理当前事件
        - 将事件向上传给上层的onTouchEvent()处理

**3.3 onInterceptTouchEvent()**

- 作用：判断是否拦截了某个事件
- 作用对象：ViewGroup
- 调用时刻：在ViewGroup的dispatchTouchEvent()内部调用
- 返回结果
    - true：当前事件被ViewGroup拦截
        - 事件停止往下传递
        - ViewGroup自己处理事件，调用父类super.dispatchTouchEvent()，最终执行自己的onTouchEvent()
    - false（默认）：当前事件没有被ViewGroup拦截
        - 事件继续往下传递
        - 事件传到子View，调用View.dispatchTouchEvent()方法去处理

**3.4 三者关系**

----伪代码阐述上述3个方法的关系 & 事件传递规则
```
/**
 * 点击事件产生后
 */ 
// 步骤1：调用dispatchTouchEvent（）
public boolean dispatchTouchEvent(MotionEvent ev) {

    //代表 是否会消费事件
    boolean consume = false; 

    // 步骤2：判断是否拦截事件
    if (onInterceptTouchEvent(ev)) {
        // a. 若拦截，则将该事件交给当前View进行处理
        // 即调用onTouchEvent (）方法去处理点击事件
        consume = onTouchEvent (ev) ;
        
    } else {
    
        // b. 若不拦截，则将该事件传递到下层
        // 即 下层元素的dispatchTouchEvent（）就会被调用，重复上述过程
        // 直到点击事件被最终处理为止
        consume = child.dispatchTouchEvent (ev) ;
    }

    // 步骤3：最终返回该事件是否被消费（接收 & 处理）
    return consume;
}
```

**onTouch()和onTouchEvent()的区别**

- 2个方法都是在View.dispatchTouchEvent（）中调用
- onTouch（）优先于onTouchEvent执行
- 若手动复写在onTouch（）中返回true（即：将事件消费掉），将不会再执行onTouchEvent（）

> 若控件不可点击（即：非enable），那么onTouch事件将不会执行

onTouch（）能够得到执行需2个前提条件：
- mOnTouchListener的值不能为空
- 当前点击的控件必须是enable的