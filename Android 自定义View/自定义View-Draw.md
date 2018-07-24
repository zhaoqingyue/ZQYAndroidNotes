### 自定义View-Draw

----绘制View视图

**1. draw过程**

View类型 | draw过程
---|---
单一View | 绘制View自身的视图
ViewGroup | 绘制View自身的视图 & 绘制所有子View的视图

**1.1 单一View的draw过程**

----继承自View、SurfaceView 或 其他View；不包含子View

**原理：**

- View绘制自身（背景 & 内容等）
- 绘制装饰

开始 --> draw() --> drawBackground() --> onDraw() --> dispatchDraw() --> onDrawScrollBars() --> 完成

- draw()
    - 绘制View自身
- drawBackground()
    - 绘制View自身的背景
- onDraw()
    - 绘制View自身的内容（需复写）
- dispatchDraw()
    - 绘制子View（空实现：没有子View）
- onDrawScrollBars()
    - 绘制装饰

----

**1.2 ViewGroup的draw过程**

----继承自ViewGroup或各种xxxLayout；含有子View

**原理：**

- ViewGroup绘制自身（背景 & 内容等）
- ViewGroup遍历子View & 绘制其所有子View
- ViewGroup绘制装饰

ViewGroup：开始 --> draw() --> drawBackground() --> onDraw() --> dispatchDraw() --> 

遍历子View：draw() --> drawBackground() --> onDraw() --> dispatchDraw() --> onDrawScrollBars() -->

回到ViewGroup：onDrawScrollBars() --> 完成

**绘制ViewGroup：**

- draw()
    - 绘制ViewGroup自身
- drawBackground()
    - 绘制ViewGroup自身的背景
- onDraw()
    - 绘制ViewGroup自身的内容（需复写）
- dispatchDraw()
    - 绘制子View（不需要复写）

**遍历绘制子View：**

- draw()
    - 绘制View自身
- drawBackground()
    - 绘制View自身的背景
- onDraw()
    - 绘制View自身的内容（需复写）
- dispatchDraw()
    - 绘制子View（空实现：没有子View）
- onDrawScrollBars()
    - 绘制装饰
    
**回到ViewGroup：**

- onDrawScrollBars()
    - 绘制装饰