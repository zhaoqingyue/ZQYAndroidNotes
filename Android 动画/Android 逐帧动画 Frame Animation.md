### Android 逐帧动画 Frame Animation

----作用对象：View

**1. 原理**

----将动画拆分为 帧 的形式，且定义每一帧 = 每一张图片。

本质：按序播放一组预先定义好的图片

**2. 使用**

**2.1 将动画资源（即每张图片资源）放到 drawable文件夹里**

----将gif动画分解成一张张图片

**2.2 设置 & 启动 动画**

    - XML 
    - Java 代码
    
**2.2.1 XML实现**

- 在 res/anim文件夹里创建动画效果animation_frame.xml文件
```
res/anim/animation_frame.xml
```
- 设置动画资源（图片资源）
```
<?xml version="1.0" encoding="utf-8"?> 
<animation-list 
    xmlns:android="http://schemas.android.com/apk/res/android" 
    // 设置是否只播放一次，默认为false 
    android:oneshot="true" > 
    
    // item = 动画图片资源；duration = 设置一帧持续时间(ms) 
    <item android:drawable="@drawable/a0" android:duration="100"/> 
    <item android:drawable="@drawable/a1" android:duration="100"/> 
    <item android:drawable="@drawable/a2" android:duration="100"/> 
    <item android:drawable="@drawable/a3" android:duration="100"/> 
    <item android:drawable="@drawable/a4" android:duration="100"/> 
    <item android:drawable="@drawable/a5" android:duration="100"/> 
    <item android:drawable="@drawable/a6" android:duration="100"/> 
    <item android:drawable="@drawable/a7" android:duration="100"/> 
    <item android:drawable="@drawable/a8" android:duration="100"/> 
    <item android:drawable="@drawable/a9" android:duration="100"/> 
    <item android:drawable="@drawable/a10" android:duration="100"/> 
    
</animation-list>
```

- 在Java代码中载入 & 启动动画
```
private Button btn_startFrame,btn_stopFrame; 
private ImageView iv; 
private AnimationDrawable animationDrawable; 

iv = (ImageView) findViewById(R.id.iv); 
btn_startFrame = (Button) findViewById(R.id.btn_startFrame); 
btn_stopFrame = (Button) findViewById(R.id.btn_stopFrame);

<-- 开始动画 --> 
btn_startFrame.setOnClickListener(new View.OnClickListener() { 

    @Override 
    public void onClick(View v) {
        // 1. 设置动画 
        iv.setImageResource(R.drawable.animation_frame); 
        
        // 2. 获取动画对象 
        animationDrawable = (AnimationDrawable) iv.getDrawable(); 
        
        // 3. 启动动画 
        animationDrawable.start(); 
    } 
}); 

//停止动画 
btn_stopFrame.setOnClickListener(new View.OnClickListener() { 
    
    @Override 
    public void onClick(View v) { 
        // 1. 设置动画 
        iv.setImageResource(R.drawable.animation_frame); 
        
        // 2. 获取动画对象 
        animationDrawable = (AnimationDrawable) iv.getDrawable(); 
        
        // 3. 暂停动画 
        animationDrawable.stop(); 
    } 
}); 
```

**2.2.2 Java代码中实现**
```
<-- 直接从drawable文件夹获取动画资源（图片） --> 
animationDrawable = new AnimationDrawable(); 
for (int i = 0; i <= 10; i++) { 
    int id = getResources().getIdentifier("a" + i, "drawable", getPackageName()); 
    Drawable drawable = getResources().getDrawable(id);
    animationDrawable.addFrame(drawable, 100); 
} 

<-- 开始动画 --> 
btn_startFrame.setOnClickListener(new View.OnClickListener() { 

    @Override 
    public void onClick(View v) { 
        animationDrawable.setOneShot(true);
        // 获取资源对象 
        iv.setImageDrawable(animationDrawable); 
        
        // 特别注意：
        // 在动画start()之前要先stop()，不然在第一次动画之后会停在最后一帧，这样动画就只会触发一次 
        animationDrawable.stop(); 
        
        // 启动动画 
        animationDrawable.start(); 
    }
    
}); 
        
<-- 停止动画 --> 
btn_stopFrame.setOnClickListener(new View.OnClickListener() { 

    @Override public void onClick(View v) {
        animationDrawable.setOneShot(true);
        iv.setImageDrawable(animationDrawable); 
        animationDrawable.stop(); 
    } 
});
```


