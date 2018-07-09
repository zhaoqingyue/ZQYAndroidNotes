### Android 属性动画 Property Animation

----作用对象：任意Java对象（不局限于视图View对象）     

Android 3.0（API 11）后出现。

**1. 原理**

----在一定时间间隔内，通过不断对值进行改变，并不断将该值赋给对象的属性，从而实现该对象在该属性上的动画效果

**2. 使用**

两个核心类：

    -ValueAnimator
    
    -ObjectAnimator
    
ObjectAnimator 与 ValueAnimator的区别：

    - ValueAnimator是先改变值，然后手动赋值给对象的属性从而实现动画；是间接对对象属性进行操作；
    
    - ObjectAnimator是先改变值，然后自动赋值给对象的属性从而实现动画；是直接对对象属性进行操作；

**2.1 ValueAnimator类**

----通过不断控制值的变化，再不断手动赋给对象的属性，从而实现动画效果。

3个重要方法：

    ValueAnimator.ofInt（int values）
    
    ValueAnimator.ofFloat（float values）
    
    ValueAnimator.ofObject（int values）

----

**2.1.1 ValueAnimator.ofInt（Object values）**

----将初始值以int形式，过渡到结束值

具体使用：

    XML 设置 
    
    Java 代码设置

- XML 设置 

    - 步骤1：在路径 res/animator的文件夹里创建相应的动画 xxx.xml文件
    ```
    res/animator/animation_property.xml
    ```
    - 步骤2：设置动画参数
    
    animation_property.xml
    ```
    // ValueAnimator采用<animator> 标签 
    <animator xmlns:android="http://schemas.android.com/apk/res/android"
    
        android:valueFrom="0"         // 初始值 
        android:valueTo="100"         // 结束值 
        android:valueType="intType"   // 变化值类型 ：floatType & intType 
        android:duration="3000"       // 动画持续时间（ms）
        android:startOffset ="1000"   // 动画延迟开始时间（ms） 
        android:fillBefore = "true"   // 动画播放完后，视图是否会停留在动画开始的状态，默认为true 
        android:fillAfter = "false"   // 动画播放完后，视图是否会停留在动画结束的状态，优先于fillBefore值，默认为false 
        android:fillEnabled= "true"   // 是否应用fillBefore值，对fillAfter值无影响，默认为true
        android:repeatMode= "restart" // 选择重复播放动画模式，restart代表正序重放，reverse代表倒序回放，默认为restart| 
        android:repeatCount = "0"     // 重放次数（所以动画的播放次数=重放次数+1），为infinite时无限重复 
        android:interpolator = @[package:]anim/interpolator_resource // 插值器，即影响动画的播放速度
    />

    ```
    - 步骤3：在Java代码中启动动画
    ```
    // 载入XML动画 
    Animator animator = AnimatorInflater.loadAnimator(context, R.animator.animation_property); 
    
    // 设置动画对象 
    animator.setTarget(view); 
    
    // 启动动画
    animator.start(); 
    ```

- Java代码设置
```
// 步骤1：设置动画属性的初始值 & 结束值 
ValueAnimator anim = ValueAnimator.ofInt(0, 3); 
// ofInt（）作用有两个 
// 1. 创建动画实例 
// 2. 将传入的多个Int参数进行平滑过渡: 此处传入0和1,表示将值从0平滑过渡到1
// 如果传入了3个Int参数 a,b,c ,则是先从a平滑过渡到b, 再从b平滑过渡到C，以此类推 
// ValueAnimator.ofInt()内置了整型估值器,直接采用默认的.不需要设置
// 即默认设置了如何从初始值 过渡到 结束值 

// 步骤2：设置动画的播放各种属性 
// 设置动画运行的时长 
anim.setDuration(500); 

// 设置动画延迟播放时间 
anim.setStartDelay(500); 

// 设置动画重复播放次数 = 重放次数+1 
// 动画播放次数 = infinite时,动画无限重复
anim.setRepeatCount(0); 

// 设置重复播放动画模式 
// ValueAnimator.RESTART(默认):正序重放 
// ValueAnimator.REVERSE:倒序回放 
anim.setRepeatMode(ValueAnimator.RESTART); 

// 步骤3：将改变的值手动赋值给对象的属性值：通过动画的更新监听器 
// 设置 值的更新监听器 
// 即：值每次改变、变化一次,该方法就会被调用一次
anim.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {

    @Override 
    public void onAnimationUpdate(ValueAnimator animation) { 
        // 获得改变后的值 
        int currentValue = (Integer) animation.getAnimatedValue(); 
        
        // 输出改变后的值 
        System.out.println(currentValue); 
        
        // 步骤4：将改变后的值赋给对象的属性值，下面会详细说明
        View.setproperty（currentValue）； 
        
        // 步骤5：刷新视图，即重新绘制，从而实现动画效果
        View.requestLayout(); 
    } 
});
// 启动动画 
anim.start(); 
```

ofInt（）源码分析 
```
// ofInt（）源码分析 
public static ValueAnimator ofInt(int... values) { 
    // 允许传入一个或多个Int参数 
    // 1. 输入一个的情况（如a）：从0过渡到a； 
    // 2. 输入多个的情况（如a，b，c）：先从a平滑过渡到b，再从b平滑过渡到C
    
    // 创建动画对象 
    ValueAnimator anim = new ValueAnimator(); 
    
    // 将传入的值赋值给动画对象 return anim; 
    anim.setIntValues(values); 
}
```

----

**2.1.2 ValueAnimator.ofFloat（float values）**

----将初始值以float形式，过渡到结束值

具体使用：

    XML 设置 
    
    Java 代码设置

- XML 设置 
    - 步骤1：在路径 res/animator的文件夹里创建相应的动画xxx.xml文件
    ```
    res/animator/animation_property.xml
    ```
    
    - 步骤2：设置动画参数
    ```
    // ValueAnimator采用<animator> 标签 
    <animator xmlns:android="http://schemas.android.com/apk/res/android" 
        
        // 设置属性同上 
        android:valueFrom="0" 
        android:valueTo="100" 
        android:valueType="floatType"
    />
    ```

    - 步骤3：在Java代码中启动动画
    
    ```
    // 载入XML动画 
    Animator animator = AnimatorInflater.loadAnimator(context, R.animator.animation_property); 
    
    // 设置动画对象 
    animator.setTarget(view); 
    
    // 启动动画
    animator.start(); 
    ```

- Java 代码设置
```
ValueAnimator anim = ValueAnimator.ofFloat(0, 3);  
// 其它使用类似ValueAnimator.ofInt（int values）
```

PS: ValueAnimator.ofInt（）与ValueAnimator.oFloat（）仅仅只是在估值器上的区别。

----

**2.1.3 ValueAnimator.ofObject（Object values）**

----将初始值以对象的形式，过渡到结束值

具体使用：
```
// 创建初始动画时的对象 & 结束动画时的对象 
myObject object1 = new myObject(); 
myObject object2 = new myObject(); 

// 创建动画对象 & 设置参数 
// 参数说明 
// 参数1：自定义的估值器对象（TypeEvaluator 类型参数）
// 参数2：初始动画的对象 
// 参数3：结束动画的对象 
ValueAnimator anim = ValueAnimator.ofObject(new myObjectEvaluator(), object1, object2); 

anim.setDuration(5000); 
anim.start();
```

具体步骤：

- 步骤1：定义对象类

如：Point.java
```
public class Point { 
    // 设置两个变量用于记录坐标的位置 
    private float x; 
    private float y; 
    
    // 构造方法用于设置坐标 
    public Point(float x, float y) { 
        this.x = x; 
        this.y = y; 
    } 
    
    // get方法用于获取坐标 
    public float getX() { 
        return x; 
    } 
    
    public float getY() { 
        return y; 
    } 
}
```

- 步骤2：根据需求实现TypeEvaluator接口

----自定义如何从初始点坐标过渡到结束点坐标

- 步骤3：将属性动画作用到自定义View当中

- 步骤4：在布局文件加入自定义View空间

- 步骤5：在主代码文件设置显示视图

----

**2.2 ObjectAnimator类**

----直接对Object的属性值进行改变操作，从而实现动画效果

具体使用：

    - XML 设置 
    
    - Java 设置

- XML 设置 

    - 步骤1：在路径 res/animator 的文件夹里创建动画效果xxx.xml文件
    ```
    res/animator/animation_set.xml
    ```

    - 步骤2：设置动画参数
    
    animation_set.xml
    ```
    // ObjectAnimator 采用<animator> 标签 
    <objectAnimator xmlns:android="http://schemas.android.com/apk/res/android"
    android:valueFrom="1"         // 初始值 
    android:valueTo="0"           // 结束值 
    android:valueType="floatType" // 变化值类型 ：floatType & intType
    android:propertyName="alpha"  // 对象变化的属性名称 />
    ```
    - 在Java代码中启动动画
    ```
    // 载入XML动画 
    Animator animator = AnimatorInflater.loadAnimator(context, R.animator.animation_set); 
    
    // 设置动画对象 
    animator.setTarget(view); 
    
    // 启动动画
    animator.start(); 
    ```
- Java 设置
```
ObjectAnimator animator = ObjectAnimator.ofFloat(Object object, String property, float ....values); 
// ofFloat()作用有两个 
// 1. 创建动画实例 
// 2. 参数设置：参数说明如下 
// Object object：需要操作的对象 
// String property：需要操作的对象的属性 
// float ....values：动画初始值 & 结束值（不固定长度） 
// 若是两个参数a, b，则动画效果则是从属性的a值到b值 
// 若是三个参数a, b, c，则则动画效果则是从属性的a值到b值再到c值 
// 以此类推 

// 设置动画运行的时长 
anim.setDuration(500); 

// 设置动画延迟播放时间 
anim.setStartDelay(500); 

// 设置动画重复播放次数 = 重放次数+1 
// 动画播放次数 = infinite时, 动画无限重复 
anim.setRepeatCount(0); 

// 设置重复播放动画模式 
// ValueAnimator.RESTART(默认):正序重放 
// ValueAnimator.REVERSE:倒序回放 
anim.setRepeatMode(ValueAnimator.RESTART); 

// 启动动画
animator.start(); 
```

使用实例：

- a. 透明度
```
// 创建动画作用对象
mButton = (Button) findViewById(R.id.Button); 

// 动画作用对象是mButton 
// 动画作用的对象的属性是透明度alpha 
// 动画效果是:常规 - 全透明 - 常规 
ObjectAnimator animator = ObjectAnimator.ofFloat(mButton, "alpha", 1f, 0f, 1f); 

animator.setDuration(5000); 
animator.start();
```

- b. 缩放
```
// 创建动画作用对象
mButton = (Button) findViewById(R.id.Button); 

// 动画作用对象是mButton 
// 动画作用的对象的属性是X轴缩放 
// 动画效果是:放大到3倍,再缩小到初始大小 
ObjectAnimator animator = ObjectAnimator.ofFloat(mButton, "scaleX", 1f, 3f, 1f); 

animator.setDuration(5000); 
animator.start();
```

- c. 平移
```
// 创建动画作用对象
mButton = (Button) findViewById(R.id.Button); 

// 获得当前按钮的位置 
float curTranslationX = mButton.getTranslationX(); 

// 动画作用对象是mButton
// 动画作用的对象的属性是X轴平移（在Y轴上平移同理，采用属性"translationY"
// 动画效果是:从当前位置平移到 x=1500 再平移到初始位置
ObjectAnimator animator = ObjectAnimator.ofFloat(mButton, 
            "translationX", curTranslationX, 300, curTranslationX); 

animator.setDuration(5000); 
animator.start();
```

- d. 旋转
```
// 创建动画作用对象
mButton = (Button) findViewById(R.id.Button);

// 动画作用对象是mButton
// 动画作用的对象的属性是旋转alpha 
// 动画效果是:0 - 360 
ObjectAnimator animator = ObjectAnimator.ofFloat(mButton, "rotation", 0f, 360f); 

animator.setDuration(5000);
animator.start();
```

属性 | 作用 | 数值类型
---|---|---
Alpha | 控制View的透明度 | float
TranslationX | 控制X方向的位移 | float
TranslationY | 控制Y方向的位移 | float
ScaleX | 控制X方向的缩放倍数 | float
ScaleY | 控制Y方向的缩放倍数 | float
Rotation | 控制以屏幕方向为轴的旋转度数 | float
RotationX | 控制以X轴为轴的旋转度数 | float
RotationY | 控制以Y轴为轴的旋转度数 | float

----

**3. 组合动画 AnimatorSet** 

    // 播放当前动画 
    AnimatorSet.play(Animator anim)   
    
    // 将现有动画延迟x毫秒后执行
    AnimatorSet.after(long delay)     
    
    // 将现有动画和传入的动画同时执行
    AnimatorSet.with(Animator anim)   
    
    // 将现有动画插入到传入的动画之后执行
    AnimatorSet.after(Animator anim)  
    
    // 将现有动画插入到传入的动画之前执行
    AnimatorSet.before(Animator anim) 

具体使用：

    XML设置 
    
    Java代码设置

- XML设置 

    - 步骤1：在 res/animator的文件夹里创建动画xxx.xml文件
    ```
    res/animator/animation_set.xml
    ```
    
    - 步骤2：设置动画效果
    
    animation_set.xml
    ```
    <?xml version="1.0" encoding="utf-8"?> 
    <set xmlns:android="http://schemas.android.com/apk/res/android"         
        android:ordering="sequentially" > 
    
        // 表示Set集合内的动画按顺序进行 
        // ordering的属性值:sequentially & together 
        // sequentially:表示set中的动画，按照先后顺序逐步进行（a 完成之后进行 b ） 
        // together:表示set中的动画，在同一时间同时进行,为默认值 
        
        <set android:ordering="together" > 
        
            // 下面的动画同时进行 
            <objectAnimator 
                android:duration="2000" 
                android:propertyName="translationX" 
                android:valueFrom="0" 
                android:valueTo="300" 
                android:valueType="floatType" > 
            </objectAnimator> 
            
            <objectAnimator 
                android:duration="3000" 
                android:propertyName="rotation" 
                android:valueFrom="0" 
                android:valueTo="360" 
                android:valueType="floatType" > 
            </objectAnimator> 
        </set> 
        
        <set android:ordering="sequentially" > 
        
            // 下面的动画按序进行 
            <objectAnimator 
                android:duration="1500" 
                android:propertyName="alpha" 
                android:valueFrom="1" 
                android:valueTo="0" 
                android:valueType="floatType" >
            </objectAnimator> 
            
            <objectAnimator 
                android:duration="1500" 
                android:propertyName="alpha" 
                android:valueFrom="0" 
                android:valueTo="1" 
                android:valueType="floatType" > 
            </objectAnimator> 
        </set>
        
    </set>
    ```
    
    - 在Java代码中启动动画
    ```
    // 创建动画作用对象
    mButton = (Button) findViewById(R.id.Button); 
    
    // 创建组合动画对象 & 加载XML动画 
    AnimatorSet animator = (AnimatorSet) AnimatorInflater.loadAnimator(this, R.animator.animation_set); 
    
    // 设置动画作用对象 
    animator.setTarget(mButton); 
    
    // 启动动画
    animator.start(); 
    ```

- Java代码设置
```
// 步骤1：设置需要组合的动画效果 
// 平移动画 
ObjectAnimator translation = ObjectAnimator.ofFloat(mButton, "translationX", 
        curTranslationX, 300,curTranslationX); 

// 旋转动画 
ObjectAnimator rotate = ObjectAnimator.ofFloat(mButton, "rotation", 0f, 360f); 

// 透明度动画 
ObjectAnimator alpha = ObjectAnimator.ofFloat(mButton, "alpha", 1f, 0f, 1f); 

// 步骤2：创建组合动画的对象 
AnimatorSet animSet = new AnimatorSet(); 

// 步骤3：根据需求组合动画
animSet.play(translation).with(rotate).before(alpha);
animSet.setDuration(5000); 

// 步骤4：启动动画 
animSet.start();
```

**4. ViewPropertyAnimator用法**

具体使用：

```
// 使用解析 

View.animate().xxx().xxx();

// ViewPropertyAnimator的功能建立在animate()上 
// 调用animate()方法返回值是一个ViewPropertyAnimator对象,之后的调用的所有方法都是通过该实例完成 
// 调用该实例的各种方法来实现动画效果 
// ViewPropertyAnimator所有接口方法都使用连缀语法来设计，每个方法的返回值都是它自身的实例 
// 因此调用完一个方法后可直接连缀调用另一方法,即可通过一行代码就完成所有动画效果 

```
例子：
```
// 单个动画设置:将按钮变成透明状态
mButton.animate().alpha(0f); 

// 单个动画效果设置 & 参数设置
mButton.animate().alpha(0f).setDuration(5000).setInterpolator(new BounceInterpolator());

// 组合动画:将按钮变成透明状态再移动到(500,500)处 
mButton.animate().alpha(0f).x(500).y(500); 

// 特别注意: 
// 动画自动启动,无需调用start()方法.因为新的接口中使用了隐式启动动画的功能，只要我们将动画定义完成后，动画就会自动启动 
// 该机制对于组合动画也同样有效，只要不断地连缀新的方法，那么动画就不会立刻执行，等到所有在ViewPropertyAnimator上设置的方法都执行完毕后，动画就会自动启动 
// 如果不想使用这一默认机制，也可以显式地调用start()方法来启动动画
```

**5. 监听动画**

----Animation类通过监听动画开始、结束、重复、取消来进行一系列操作，如跳转页面等

- addListener（）

----须重写4个方法

```
Animation.addListener(new AnimatorListener() { 
    
    // 特别注意：每次监听必须4个方法都重写
    @Override 
    public void onAnimationStart(Animation animation) { 
        // 动画开始时执行 
    } 
    
    @Override 
    public void onAnimationRepeat(Animation animation) { 
        // 动画重复时执行 
    } 
    
    @Override 
    public void onAnimationCancel(Animation animation) { 
        // 动画取消时执行 
    } 
    
    @Override 
    public void onAnimationEnd(Animation animation) { 
        // 动画结束时执行 
    } 
});
```
- AnimatorListenerAdapter

----监听想要的方法
```
anim.addListener(new AnimatorListenerAdapter() { 
    // 向addListener()方法中传入适配器对象AnimatorListenerAdapter() 
    
    @Override 
    public void onAnimationStart(Animator animation) { 
        // 如想只想监听动画开始时刻，只需单独重写该方法即可
    } 
});
```


