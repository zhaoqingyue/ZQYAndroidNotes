### 自定义View

----View是显示在屏幕上的各种视图

> Android中的UI组件由View、ViewGroup组成。

**1. View的分类**

----视图View主要分为两类：

类别 | 描述 | 特点
---|---|---
单一视图 | 一个View，如：TextView、Button | 不包含子View
视图组 | 多个View组成的ViewGroup，如：LinearLayout、RelativeLayout | 包含子View

**2. View类**

----View是Android中各种组件的基类，如：View是ViewGroup基类

View的构造函数（共4个）：

>自定义View必须重写至少一个构造函数

```
/**
 * View在Java代码里面new的，则调用第一个构造函数
 */
public CarsonView(Context context) {
    super(context);
}

/**
 * View是在.xml里声明的，则调用第二个构造函数
 * 自定义属性是从AttributeSet参数传进来的
 */
public  CarsonView(Context context, AttributeSet attrs) {
    super(context, attrs);
}

/**
 * 不会自动调用
 * 一般是在第二个构造函数里主动调用
 * View有style属性时
 */
public  CarsonView(Context context, AttributeSet attrs, int defStyleAttr) {
    super(context, attrs, defStyleAttr);
}

/**
 * API21之后才使用
 * 不会自动调用
 * 一般是在第二个构造函数里主动调用
 * View有style属性时
 */
public  CarsonView(Context context, AttributeSet attrs, int defStyleAttr, int defStyleResource) {
    super(context, attrs, defStyleAttr, defStyleResource);
}
```
- Context：上下文
- AttributeSet：XML属性（当从XML inflate的时候）
- int defStyleAttr：应用到View的默认风格（定义在主题中）
- int defStyleResource： 如果没有使用defStyleAttr，应用到View的默认风格

**3. View视图结构**

----结构是树形结构，最顶层是ViewGroup，ViewGroup下可能有多个ViewGroup或View

![image](https://note.youdao.com/favicon.ico)

**注意：**

----无论是measure过程 & layout过程 & draw过程，都是从View树的根节点开始测量 & 计算 & 绘制的（即从树的顶端开始），一层一层、一个分支一个分支地进行（即树形递归），最终计算整个View树中各个View，确定整个View树的相关属性。

**4. Android坐标系**

Android坐标系定义为：

- 屏幕的左上角为坐标原点
- 向右为x轴增大方向
- 向下为y轴增大方向

![image](https://note.youdao.com/favicon.ico)

**5. View位置（坐标）**

View的位置由4个顶点决定的（如下A、B、C、D）

![image](https://note.youdao.com/favicon.ico)

4个顶点的位置(View的位置是相对于父控件而言的)

![image](https://note.youdao.com/favicon.ico)

- Top：子View左上角距父View顶部的距离；
- Left：子View左上角距父View左侧的距离；
- Bottom：子View右下角距父View顶部的距离
- Right：子View右下角距父View左侧的距离

**6. 位置获取方式**

View的位置是通过view.getxxx()函数进行获取
```
// 获取子View左上角距父View顶部的距离
getTop()    

// 获取子View左上角距父View左侧的距离
getLeft()   

// 获取子View右下角距父View顶部的距离
getBottom() 

// 获取子View右下角距父View左侧的距离
getRight()  
```

**7. Android中颜色**

**7.1 颜色模式**

Android支持的颜色模式：

- ARGB8888：4通道高精度（32位）
- ARGB4444：4通道低精度（16位）
- RGB565：Android屏幕默认模式（16位）
- Alpha8：仅有透明通道（8位）

字母：表示通道类型

数值：表示该类型用多少位二进制来描述

例如：ARGB8888的颜色定义

类型 | 描述 | 取值范围
---|---|---
A（Alpha）| 透明度 | 透明 ~ 不透明
R（Red）| 红色 | 无色 ~ 红色
G (Green) | 绿色 | 无色 ~ 绿色
B (Blue) | 蓝色 | 无色 ~ 蓝色

**7.2 定义颜色的方式**

- 在Java中定义颜色
```
// Java中使用Color类定义颜色
int color = Color.GRAY; // 灰色

//Color类使用ARGB值进行表示
int color = Color.argb(127, 255, 0, 0); // 半透明红色
int color = 0xaaff0000;                 // 带有透明度的红色
```

- 在xml文件中定义颜色

    - 在/res/values/color.xml 文件中定义
    ```
    // 定义红色（没有alpha通道）
    <color name="red">#ff0000</color>
    
    // 定义蓝色（没有alpha通道）
    <color name="green">#00ff00</color>
    ```

    - 在xml文件中以"#"开头定义颜色，后面跟十六进制的值
    ```
    #f00        // 低精度 - 不带透明通道红色
    #af00       // 低精度 - 带透明通道红色

    #ff0000     // 高精度 - 不带透明通道红色
    #aaff0000   // 高精度 - 带透明通道红色
    ```

**7.3 引用颜色的方式**

- 在Java文件中引用xml中定义的颜色
```
// 方法1
int color = getResources().getColor(R.color.mycolor);

// 方法2（API 23及以上）
int color = getColor(R.color.myColor);   
```

- 在xml文件(layout或style)中引用或者创建颜色
```
<!--在style文件中引用-->
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
    <item name="colorPrimary">@color/red</item>
</style>

<!--在layout文件中引用在/res/values/color.xml中定义的颜色-->
android:background="@color/red"     

<!--在layout文件中创建并使用颜色-->
android:background="#ff0000"  
```
