### Android 补间动画 Tween Animation

----作用对象：**View**

- Alpha Animation 透明度动画
- Scale Animation 缩放动画
- Translate Animation 平移动画
- Rotate Animation 旋转动画

**1. 原理**

----通过确定开始的视图样式 、 结束的视图样式 & 中间动画变化过程， 由系统补全来确定一个动画。

动画类型 | 原理 | Animation子类
---|---|---
透明度动画（Alpha） | 改变视图的透明度 | AlphaAnimation
缩放动画（Scale） | 放大或缩小视图的大小 | ScaleAnimation
平移动画（Translate） | 平移视图的位置 | TranslateAnimation
旋转动画（Rotate） | 旋转视图的角度 | RotateAnimation

**2. 使用**

----补间动画的使用方式：

- XML 代码设置
- Java 代码设置（动画效果可动态创建）

----

**2.1 透明度动画（Alpha）**

**2.1.1 XML 代码设置**

a. 在路径 res/anim的文件夹里创建 animation_alpha.xml 文件
```
res/anim/animation_alpha.xml
```

b. 根据不同的动画效果，设置不同动画参数，从而实现动画效果
```
<?xml version="1.0" encoding="utf-8"?> 
// 采用<alpha/> 标签表示是透明度动画 
<alpha xmlns:android="http://schemas.android.com/apk/res/android" 

    // 以下参数是4种动画效果的公共属性, 即都有的属性 
    android:duration="3000"       // 动画持续时间（ms），必须设置，动画才有效果 
    android:startOffset ="1000"   // 动画延迟开始时间（ms） 
    android:fillBefore = "true"   // 动画播放完后，视图是否会停留在动画开始的状态，默认为true 
    android:fillAfter = "false"   // 动画播放完后，视图是否会停留在动画结束的状态，优先于fillBefore值，默认为false 
    android:fillEnabled= "true"   // 是否应用fillBefore值，对fillAfter值无影响，默认为true 
    android:repeatMode= "restart" // 选择重复播放动画模式，restart代表正序重放，reverse代表倒序回放，默认为restart
    android:repeatCount = "0"     // 重放次数（所以动画的播放次数=重放次数+1），为infinite时无限重复 
    android:interpolator = @[package:]anim/interpolator_resource // 插值器，即影响动画的播放速度
    
    // 以下参数是透明度动画特有的属性 
    android:fromAlpha="1.0" // 动画开始时视图的透明度(取值范围: -1 ~ 1) 
    android:toAlpha="0.0"   // 动画结束时视图的透明度(取值范围: -1 ~ 1) 
/>
```

c. 在Java代码中创建Animation对象并播放动画
```
// 步骤1:创建 需要设置动画的 视图View
Button mButton = (Button) findViewById(R.id.Button); 

// 步骤2:创建 动画对象 并传入设置的动画效果xml文件
Animation alphaAnimation = AnimationUtils.loadAnimation(this, R.anim.animation_alpha);  

// 步骤3:播放动画
mButton.startAnimation(alphaAnimation); 
```

**2.1.2 Java 代码设置**
```
// 步骤1:创建 需要设置动画的 视图View 
Button mButton = (Button) findViewById(R.id.Button); 

// 步骤2：创建透明度动画对象 & 设置动画效果：透明度动画对应的Animation子类为AlphaAnimation 
// 参数说明: 
// 1. fromAlpha:动画开始时视图的透明度(取值范围: -1 ~ 1) 
// 2. toAlpha:动画结束时视图的透明度(取值范围: -1 ~ 1) 
Animation alphaAnimation = new AlphaAnimation(1, 0); 

// 固定属性的设置都是在其属性前加 "set"，如setDuration（） 
alphaAnimation.setDuration(3000); 

// 步骤3：播放动画
mButton.startAnimation(alphaAnimation); 
```
----

**2.2 缩放动画（Scale）**

**2.2.1 XML 代码设置**

a. 在路径 res/anim的文件夹里创建 animation_scale.xml 文件
```
res/anim/animation_scale.xml
```

b. 根据不同的动画效果，设置不同动画参数，从而实现动画效果
```
<?xml version="1.0" encoding="utf-8"?> 
// 采用<scale/> 标签表示是缩放动画 
<scale xmlns:android="http://schemas.android.com/apk/res/android" 

    // 以下参数是4种动画效果的公共属性, 即都有的属性 
    android:duration="3000"       // 动画持续时间（ms），必须设置，动画才有效果 
    android:startOffset ="1000"   // 动画延迟开始时间（ms） 
    android:fillBefore = "true"   // 动画播放完后，视图是否会停留在动画开始的状态，默认为true 
    android:fillAfter = "false"   // 动画播放完后，视图是否会停留在动画结束的状态，优先于fillBefore值，默认为false 
    android:fillEnabled= "true"   // 是否应用fillBefore值，对fillAfter值无影响，默认为true 
    android:repeatMode= "restart" // 选择重复播放动画模式，restart代表正序重放，reverse代表倒序回放，默认为restart
    android:repeatCount = "0"     // 重放次数（所以动画的播放次数=重放次数+1），为infinite时无限重复 
    android:interpolator = @[package:]anim/interpolator_resource // 插值器，即影响动画的播放速度
    
    // 以下参数是缩放动画特有的属性
    android:fromXScale="0.0" // 动画在水平方向X的起始缩放倍数 
    // 0.0表示收缩到没有；1.0表示正常无伸缩 
    // 值小于1.0表示收缩；值大于1.0表示放大 
    
    android:toXScale="2"     // 动画在水平方向X的结束缩放倍数 
    android:fromYScale="0.0" // 动画开始前在竖直方向Y的起始缩放倍数 
    android:toYScale="2"     // 动画在竖直方向Y的结束缩放倍数 
    android:pivotX="50%"     // 缩放轴点的x坐标 
    android:pivotY="50%"     // 缩放轴点的y坐标 
    
    // 轴点 = 视图缩放的中心点 
    // pivotX pivotY, 可取值为数字，百分比，或者百分比p 
    
    // 设置为数字时（如50），轴点为View的左上角的原点在x方向和y方向加上50px的点。
    // 在Java代码里面设置这个参数的对应参数是Animation.ABSOLUTE。 
    
    // 设置为百分比时（如50%），轴点为View的左上角的原点在x方向加上自身宽度50%和y方向自身高度50%的点。
    // 在Java代码里面设置这个参数的对应参数是Animation.RELATIVE_TO_SELF。 
    
    // 设置为百分比p时（如50%p），轴点为View的左上角的原点在x方向加上父控件宽度50%和y方向父控件高度50%的点。
    // 在Java代码里面设置这个参数的对应参数是Animation.RELATIVE_TO_PARENT 
/>
```

c. 在Java代码中创建Animation对象并播放动画
```
// 步骤1:创建 需要设置动画的 视图View 
Button mButton = (Button) findViewById(R.id.Button); 

/ 步骤2:创建 动画对象 并传入设置的动画效果xml文件 
Animation scaleAnimation = AnimationUtils.loadAnimation(this, R.anim.animation_scale); 
/
// 步骤3:播放动画
mButton.startAnimation(scaleAnimation); 
```

**2.2.2 Java 代码设置**
```
// 步骤1:创建 需要设置动画的 视图View 
Button mButton = (Button) findViewById(R.id.Button); 

// 步骤2：创建缩放动画的对象 & 设置动画效果：缩放动画对应的Animation子类为RotateAnimation 
Animation scaleAnimation = new ScaleAnimation(0, 2, 0, 2, Animation.RELATIVE_TO_SELF, 0.5f, Animation.RELATIVE_TO_SELF, 0.5f);

// 参数说明: 
// 1. fromX ：动画在水平方向X的结束缩放倍数 
// 2. toX ：动画在水平方向X的结束缩放倍数 
// 3. fromY ：动画开始前在竖直方向Y的起始缩放倍数 
// 4. toY：动画在竖直方向Y的结束缩放倍数 
// 5. pivotXType:缩放轴点的x坐标的模式 
// 6. pivotXValue:缩放轴点x坐标的相对值 
// 7. pivotYType:缩放轴点的y坐标的模式 
// 8. pivotYValue:缩放轴点y坐标的相对值 
// pivotXType = Animation.ABSOLUTE:缩放轴点的x坐标 = View左上角的原点 在x方向 加上 pivotXValue数值的点(y方向同理) 
// pivotXType = Animation.RELATIVE_TO_SELF:缩放轴点的x坐标 = View左上角的原点 在x方向 加上 自身宽度乘上pivotXValue数值的值(y方向同理) 
// pivotXType = Animation.RELATIVE_TO_PARENT:缩放轴点的x坐标 = View左上角的原点 在x方向 加上 父控件宽度乘上pivotXValue数值的值 (y方向同理) 

// 固定属性的设置都是在其属性前加"set"，如setDuration（） 
scaleAnimation.setDuration(3000); 

// 步骤3：播放动画
mButton.startAnimation(scaleAnimation); 
```
----

**2.3 平移动画（Translate）**

**2.3.1 XML 代码设置**

a. 在路径 res/anim的文件夹里创建 animation_translate.xml 文件
```
res/anim/animation_translate.xml
```

b. 根据不同的动画效果，设置不同动画参数，从而实现动画效果
```
<?xml version="1.0" encoding="utf-8"?> 
// 采用<translate /> 标签表示是平移动画
<translate  xmlns:android="http://schemas.android.com/apk/res/android" 

    // 以下参数是4种动画效果的公共属性, 即都有的属性 
    android:duration="3000"       // 动画持续时间（ms），必须设置，动画才有效果 
    android:startOffset ="1000"   // 动画延迟开始时间（ms） 
    android:fillBefore = "true"   // 动画播放完后，视图是否会停留在动画开始的状态，默认为true 
    android:fillAfter = "false"   // 动画播放完后，视图是否会停留在动画结束的状态，优先于fillBefore值，默认为false 
    android:fillEnabled= "true"   // 是否应用fillBefore值，对fillAfter值无影响，默认为true 
    android:repeatMode= "restart" // 选择重复播放动画模式，restart代表正序重放，reverse代表倒序回放，默认为restart
    android:repeatCount = "0"     // 重放次数（所以动画的播放次数=重放次数+1），为infinite时无限重复 
    android:interpolator = @[package:]anim/interpolator_resource // 插值器，即影响动画的播放速度
    
    // 以下参数是平移动画特有的属性 
    android:fromXDelta="0" // 视图在水平方向x 移动的起始值 
    android:toXDelta="500" // 视图在水平方向x 移动的结束值 
    android:fromYDelta="0" // 视图在竖直方向y 移动的起始值 
    android:toYDelta="500" // 视图在竖直方向y 移动的结束值
/>
```

c. 在Java代码中创建Animation对象并播放动画
```
// 步骤1:创建 需要设置动画的 视图View 
Button mButton = (Button) findViewById(R.id.Button); 
 
// 步骤2:创建 动画对象 并传入设置的动画效果xml文件
Animation translateAnimation = AnimationUtils.loadAnimation(this, R.anim.translate_animation); 
 
// 步骤3:播放动画
mButton.startAnimation(translateAnimation); 
```

**2.3.2 Java 代码设置**
```
// 步骤1:创建 需要设置动画的 视图View 
Button mButton = (Button) findViewById(R.id.Button); 

// 步骤2：创建平移动画的对象：平移动画对应的Animation子类为TranslateAnimation 
// 参数分别是： 
// 1. fromXDelta ：视图在水平方向x 移动的起始值 
// 2. toXDelta ：视图在水平方向x 移动的结束值 
// 3. fromYDelta ：视图在竖直方向y 移动的起始值 
// 4. toYDelta：视图在竖直方向y 移动的结束值 
Animation translateAnimation = new TranslateAnimation(0，500，0，500); 

// 固定属性的设置都是在其属性前加"set"，如setDuration（） 
translateAnimation.setDuration(3000); 

// 步骤3:播放动画
mButton.startAnimation(translateAnimation); 
```
----

**2.4 旋转动画（Rotate）**

**2.4.1 XML 代码设置**

a. 在路径 res/anim的文件夹里创建 animation_rotate.xml 文件
```
res/anim/animation_rotate.xml
```

b. 根据不同的动画效果，设置不同动画参数，从而实现动画效果
```
<?xml version="1.0" encoding="utf-8"?> 
// 采用<rotate/> 标签表示是旋转动画
<rotate xmlns:android="http://schemas.android.com/apk/res/android" 

    // 以下参数是4种动画效果的公共属性, 即都有的属性 
    android:duration="3000"       // 动画持续时间（ms），必须设置，动画才有效果 
    android:startOffset ="1000"   // 动画延迟开始时间（ms） 
    android:fillBefore = "true"   // 动画播放完后，视图是否会停留在动画开始的状态，默认为true 
    android:fillAfter = "false"   // 动画播放完后，视图是否会停留在动画结束的状态，优先于fillBefore值，默认为false 
    android:fillEnabled= "true"   // 是否应用fillBefore值，对fillAfter值无影响，默认为true 
    android:repeatMode= "restart" // 选择重复播放动画模式，restart代表正序重放，reverse代表倒序回放，默认为restart
    android:repeatCount = "0"     // 重放次数（所以动画的播放次数=重放次数+1），为infinite时无限重复 
    android:interpolator = @[package:]anim/interpolator_resource // 插值器，即影响动画的播放速度
    
    // 以下参数是旋转动画特有的属性 
    android:fromDegrees="0" // 动画开始时 视图的旋转角度(正数 = 顺时针，负数 = 逆时针) 
    android:toDegrees="270" // 动画结束时 视图的旋转角度(正数 = 顺时针，负数 = 逆时针) 
    android:pivotX="50%"    // 旋转轴点的x坐标 
    android:pivotY="0"      // 旋转轴点的y坐标 
    
    // 轴点 = 视图缩放的中心点 
    // pivotX pivotY,可取值为数字，百分比，或者百分比p 
    // 设置为数字时（如50），轴点为View的左上角的原点在x方向和y方向加上50px的点。
    // 在Java代码里面设置这个参数的对应参数是Animation.ABSOLUTE。 
    
    // 设置为百分比时（如50%），轴点为View的左上角的原点在x方向加上自身宽度50%和y方向自身高度50%的点。
    // 在Java代码里面设置这个参数的对应参数是Animation.RELATIVE_TO_SELF。 
    
    // 设置为百分比p时（如50%p），轴点为View的左上角的原点在x方向加上父控件宽度50%和y方向父控件高度50%的点。
    // 在Java代码里面设置这个参数的对应参数是Animation.RELATIVE_TO_PARENT 
/>
```

c. 在Java代码中创建Animation对象并播放动画
```
// 步骤1:创建 需要设置动画的 视图View 
Button mButton = (Button) findViewById(R.id.Button); 
 
// 步骤2:创建 动画对象 并传入设置的动画效果xml文件 
Animation rotateAnimation = AnimationUtils.loadAnimation(this, R.anim.rotate_animation); 
 
// 步骤3:播放动画
mButton.startAnimation(rotateAnimation); 
```

**2.4.2 Java 代码设置**
```
// 步骤1:创建 需要设置动画的 视图View 
Button mButton = (Button) findViewById(R.id.Button); 

Animation rotateAnimation = new RotateAnimation(0, 270, Animation.RELATIVE_TO_SELF, 0.5f, Animation.RELATIVE_TO_SELF, 0.5f); 
// 步骤2：创建旋转动画的对象 & 设置动画效果：旋转动画对应的Animation子类为RotateAnimation 
// 参数说明: 
// 1. fromDegrees ：动画开始时 视图的旋转角度(正数 = 顺时针，负数 = 逆时针) 
// 2. toDegrees ：动画结束时 视图的旋转角度(正数 = 顺时针，负数 = 逆时针) 
// 3. pivotXType：旋转轴点的x坐标的模式 
// 4. pivotXValue：旋转轴点x坐标的相对值 
// 5. pivotYType：旋转轴点的y坐标的模式 
// 6. pivotYValue：旋转轴点y坐标的相对值 
// pivotXType = Animation.ABSOLUTE:旋转轴点的x坐标 = View左上角的原点 在x方向 加上 pivotXValue数值的点(y方向同理) 
// pivotXType = Animation.RELATIVE_TO_SELF:旋转轴点的x坐标 = View左上角的原点 在x方向 加上 自身宽度乘上pivotXValue数值的值(y方向同理) 
// pivotXType = Animation.RELATIVE_TO_PARENT:旋转轴点的x坐标 = View左上角的原点 在x方向 加上 父控件宽度乘上pivotXValue数值的值 (y方向同理) 

// 固定属性的设置都是在其属性前加"set"，如setDuration（） 
rotateAnimation.setDuration(3000); 

// 步骤3：播放动画
mButton.startAnimation(rotateAnimation); 
```
----

**2.5 组合动画**

----同时使用平移、缩放、旋转 、透明度4种动画的组合，即组合动画。

使用组合动画需要用到标签 < Set />

**2.5.1 XML 代码设置**

a. 在路径 res/anim的文件夹里创建 animation_set.xml 文件
```
res/anim/animation_set.xml
```

b. 根据不同的动画效果，设置不同动画参数，从而实现动画效果
```
<?xml version="1.0" encoding="utf-8"?> 
// 采用<set/> 标签表示是组合动画 
<set xmlns:android="http://schemas.android.com/apk/res/android" 

    // 以下参数是4种动画效果的公共属性, 即都有的属性 
    android:duration="3000"       // 动画持续时间（ms），必须设置，动画才有效果 
    android:startOffset ="1000"   // 动画延迟开始时间（ms） 
    android:fillBefore = "true"   // 动画播放完后，视图是否会停留在动画开始的状态，默认为true 
    android:fillAfter = "false"   // 动画播放完后，视图是否会停留在动画结束的状态，优先于fillBefore值，默认为false 
    android:fillEnabled= "true"   // 是否应用fillBefore值，对fillAfter值无影响，默认为true 
    android:repeatMode= "restart" // 选择重复播放动画模式，restart代表正序重放，reverse代表倒序回放，默认为restart
    android:repeatCount = "0"     // 重放次数（所以动画的播放次数=重放次数+1），为infinite时无限重复 
    android:interpolator = @[package:]anim/interpolator_resource // 插值器，即影响动画的播放速度
    
    // 组合动画独特的属性 
    android:shareinterpolator="true" 
    // 表示组合动画中的动画是否和集合共享同一个差值器 
    // 如果集合不指定插值器，那么子动画需要单独设置 
    
    // 组合动画播放时是全部动画同时开始 
    // 如果想不同动画不同时间开始就要使用android:startOffset属性来延迟单个动画播放时间 
    
    // 设置旋转动画，语法同单个动画 
    <rotate 
        android:duration="1000" 
        android:fromDegrees="0" 
        android:toDegrees="360" 
        android:pivotX="50%" 
        android:pivotY="50%" 
        android:repeatMode="restart" 
        android:repeatCount="infinite" /> 
        
    // 设置平移动画，语法同单个动画 
    <translate 
        android:duration="10000" 
        android:startOffset = "1000" // 延迟该动画播放时间 
        android:fromXDelta="-50%p" 
        android:fromYDelta="0" 
        android:toXDelta="50%p" 
        android:toYDelta="0" /> 
            
    // 设置透明度动画，语法同单个动画 
    <alpha 
        android:startOffset="7000" 
        android:duration="3000" 
        android:fromAlpha="1.0" 
        android:toAlpha="0.0" /> 
        
    // 设置缩放动画，语法同单个动画 
    <scale 
        android:startOffset="4000" 
        android:duration="1000" 
        android:fromXScale="1.0" 
        android:fromYScale="1.0" 
        android:pivotX="50%" 
        android:pivotY="50%" 
        android:toXScale="0.5" 
        android:toYScale="0.5" /> 
        
        // 特别注意： 
        // 1. 在组合动画里scale缩放动画设置的repeatCount（重复播放）和fillBefore（播放完后，视图是否会停留在动画开始的状态）是无效的。 
        // 2. 所以如果需要重复播放或者回到原位的话需要在set标签里设置 
        // 3. 但是由于此处rotate旋转动画里已设置repeatCount为infinite，所以动画不会结束，也就看不到重播和回复原位
/>
```
c. 在Java代码中创建Animation对象并播放动画
```
// 步骤1:创建 需要设置动画的 视图View
Button mButton = (Button) findViewById(R.id.Button); 

// 步骤2:创建 动画对象 并传入设置的动画效果xml文件
AnimationSet setAnimation = AnimationUtils.loadAnimation(this, R.anim.animation_set);  

// 步骤3:播放动画
mButton.startAnimation(setAnimation); 
```

**2.5.2 Java 代码设置**
```
// 创建 需要设置动画的 视图View 
Button mButton = (Button) findViewById(R.id.Button); 

// ---- 组合动画设置 ----

// 步骤1:创建组合动画对象(设置为true) 
AnimationSet setAnimation = new AnimationSet(true); 

// 步骤2:设置组合动画的属性 

// 特别说明以下情况 
// 因为在下面的旋转动画设置了无限循环(RepeatCount = INFINITE) 
// 所以动画不会结束，而是无限循环 
// 所以组合动画的下面两行设置是无效的 
setAnimation.setRepeatMode(Animation.RESTART); 
// 设置了循环一次,但无效 
setAnimation.setRepeatCount(1);

// 步骤3:逐个创建子动画(方式同单个动画创建方式,此处不作过多描述) 

// 子动画1:旋转动画 
Animation rotate = new RotateAnimation(0, 360, Animation.RELATIVE_TO_SELF, 0.5f, 
    Animation.RELATIVE_TO_SELF, 0.5f); 
rotate.setDuration(1000); 
rotate.setRepeatMode(Animation.RESTART); 
rotate.setRepeatCount(Animation.INFINITE); 

// 子动画2:平移动画 
Animation translate = new TranslateAnimation(TranslateAnimation.RELATIVE_TO_PARENT, -0.5f, 
    TranslateAnimation.RELATIVE_TO_PARENT, 0.5f, 
    TranslateAnimation.RELATIVE_TO_SELF, 0,
    TranslateAnimation.RELATIVE_TO_SELF, 0); 
translate.setDuration(10000); 

// 子动画3:透明度动画 
Animation alpha = new AlphaAnimation(1, 0); 
alpha.setDuration(3000); 
alpha.setStartOffset(7000); 

// 子动画4:缩放动画 
Animation scale1 = new ScaleAnimation(1, 0.5f, 1, 0.5f, Animation.RELATIVE_TO_SELF, 0.5f, 
    Animation.RELATIVE_TO_SELF, 0.5f); 
scale.setDuration(1000); 
scale.setStartOffset(4000); 

// 步骤4:将创建的子动画添加到组合动画里 
setAnimation.addAnimation(alpha); 
setAnimation.addAnimation(rotate); 
setAnimation.addAnimation(translate); 
setAnimation.addAnimation(scale); 

// 步骤5:播放动画
mButton.startAnimation(setAnimation); 
```

**3. 监听动画**

----Animation类通过监听动画开始、结束、重复来进行相关逻辑处理，如跳转页面等。

**添加监听设置：**

- setAnimationListener()
```
animation.setAnimationListener(new Animation.AnimationListener() { 

    @Override 
    public void onAnimationStart(Animation animation) { 
        // 动画开始时回调 
    } 
    
    @Override 
    public void onAnimationEnd(Animation animation) { 
        // 动画结束时回调 
    } 
    
    @Override 
    public void onAnimationRepeat(Animation animation) { 
        //动画重复执行时回调 
    } 
});
```
- addListener()
```
animation.addListener(new AnimatorListenerAdapter() { 

    // 向addListener()方法中传入适配器对象AnimatorListenerAdapter() 
    
    @Override 
    public void onAnimationStart(Animator animation) { 
        // 如：只想监听动画开始时刻，单独重写该方法即可 
    } 
});
```
