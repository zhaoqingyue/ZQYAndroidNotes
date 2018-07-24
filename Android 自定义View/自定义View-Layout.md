### 自定义View-Layout

----计算视图（View）的位置：Left、Top、Right 和 Bottom

**1. layout过程**

View类型 | layout过程
---|---
单一View | 计算View自身的位置
ViewGroup | 计算View自身的位置 & 确定子View在父视图中的位置

**1.1 单一View的layout过程**

----继承自View、SurfaceView或其它View；不包含子View

开始 --> layout() --> onLayout() --> 完成

- layout()
    - 计算自身View的位置：调用setFrame() & setOpticalFrame()
        - 判断当前View大小和位置是否发生了变化
        - setFrame()：根据传入的4个位置值，设置View自身的四个顶点位置
        - setOpticalFrame()：根据传入的4个位置值，设置View自身的四个顶点位置
- onLayout()
    - 由于单一View没有子View，故onLayout（）是一个空实现

----

**1.2 ViewGroup的layout过程**

----继承自ViewGroup或各种xxxLayout；含有子View

**原理：**

- 计算自身ViewGroup的位置：layout()
- 遍历子View & 确定自身子View在ViewGroup的位置( 调用子View 的 layout() )：onLayout() 

开始 --> layout() --> onLayout() --> 遍历子View --> 完成

> 自上而下、一层一层地传递下去，直到完成整个View树的layout（）过程

- layout()
    - 计算父View的位置
    - 调用setFrame()
- onLayout()
    - 确定子View在父视图中的位置
    - 复写
    - 调用子View的layout()
- 遍历子View
    - 重复 "单一View的layout过程"

**2. getWidth() & getHeight() 与 getMeasuredWidth() & getMeasuredHeight()**

- getWidth() & getHeight()
    - 获得View最终的宽 & 高
    - layout()过程
- getMeasuredWidth() & getMeasuredHeight()
    - 获得 View测量的宽 & 高
    - measure()过程