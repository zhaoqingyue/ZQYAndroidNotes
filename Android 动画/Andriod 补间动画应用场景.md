### Andriod 补间动画-应用场景

- Activity 的切换效果
- Fragement 的切换效果
- 视图组（ViewGroup）中子元素的出场效果

**1. Activity 的切换效果**

----Activity 启动 / 退出时的动画效果。

- 启动动画
```
Intent intent = new Intent (this, B_Acvtivity.class); 
startActivity(intent); 

// 采用overridePendingTransition（int enterAnim, int exitAnim）进行设置 
overridePendingTransition(R.anim.enter_anim,R.anim.exit_anim); 
// enterAnim：从Activity A 跳转到Activity B，进入b时的动画效果资源ID 
// exitAnim： 从Activity A 跳转到Activity B，离开a时的动画效果资源Id 

// 特别注意 
// overridePendingTransition（）必须要在startActivity(intent)后被调用才能生效
```

- 退出动画
```
finish();

// 采用overridePendingTransition（int enterAnim, int exitAnim）进行设置 
overridePendingTransition(R.anim.enter_anim,R.anim.exit_anim); 
// enterAnim：从Activity A 跳转到Activity B，进入b时的动画效果资源ID 
// exitAnim： 从Activity A 跳转到Activity B，离开a时的动画效果资源Id 

// 特别注意 
// overridePendingTransition（）必须要在finish()后被调用才能生效 
```

enterAnim & exitAnim 的资源ID，系统有自带的效果android.R.anim.xxx。如：
```
Intent intent = new Intent(this, SecActivity.class); 
startActivity(intent); 

// 淡入淡出的动画效果 
overridePendingTransition(android.R.anim.fade_in, android.R.anim.fade_out); 

// 从左向右滑动的效果 
overridePendingTransition(android.R.anim.slide_in_left, android.R.anim.slide_out_right);
```

除了系统自带的切换效果，可以使用**补间动画**自定义Activity的切换效果：

- 自定义淡入淡出效果

----淡入淡出 效果是采用透明度动画（Alpha）

fade_in.xml（淡入）
```
<?xml version="1.0" encoding="utf-8"?> 
<set xmlns:android="http://schemas.android.com/apk/res/android" > 

<alpha 
    android:duration="1500" 
    android:fromAlpha="0.0" 
    android:toAlpha="1.0" /> 
    
</set>
```
fade_out.xml（淡出）
```
<?xml version="1.0" encoding="utf-8"?> 
<set xmlns:android="http://schemas.android.com/apk/res/android" > 

<alpha 
    android:duration="1500" 
    android:fromAlpha="1.0" 
    android:toAlpha="0.0" /> 
    
</set>
```

在Java代码中设置
```
Intent intent = new Intent(this, SecActivity.class); 
startActivity(intent); 

// 自定义的淡入淡出动画效果 
overridePendingTransition(R.anim.fade_in, R.anim.fade_out);
```

- 自定义左右滑动效果

----左右滑动 效果是采用平移动画（Translate）

out_to_left.xml(从中间滑到左边)
```
<?xml version="1.0" encoding="utf-8"?> 
<set xmlns:android="http://schemas.android.com/apk/res/android" > 

<translate 
    android:duration="500" 
    android:fromXDelta="0%p" 
    android:toXDelta="-100%p" />

</set>
```

in_from_right.xml(从右边滑到中间)
```
<?xml version="1.0" encoding="utf-8"?> 
<set xmlns:android="http://schemas.android.com/apk/res/android" > 

<translate 
    android:duration="500" 
    android:fromXDelta="100%p" 
    android:toXDelta="0%p" /> 

</set>
```

在Java代码中设置效果
```
Intent intent = new Intent(this, SecActivity.class); 
startActivity(intent) 

// 自定义从右向左滑动的效果 
overridePendingTransition(R.anim.in_from_right, R.anim.out_to_left);
```

**2. Fragment动画切换效果**

- 系统自带的动画切换效果
```
FragmentTransaction fragmentTransaction = mFragmentManager .beginTransaction(); fragmentTransaction.setTransition(int transit)； 

// 通过setTransition(int transit)进行设置 
// transit参数说明 
// 1. FragmentTransaction.TRANSIT_NONE：无动画 
// 2. FragmentTransaction.TRANSIT_FRAGMENT_OPEN：标准的打开动画效果 
// 3. FragmentTransaction.TRANSIT_FRAGMENT_CLOSE：标准的关闭动画效果 

// 标准动画设置好后，在Fragment添加和移除的时候都会有。
```

- 自定义动画效果
```
// 采用 FragmentTransavtion 的 setCustomAnimations（）进行设置 
FragmentTransaction fragmentTransaction = mFragmentManager .beginTransaction(); fragmentTransaction.setCustomAnimations(R.anim.in_from_right, R.anim.out_to_left);
```

**3. 视图组（ViewGroup）中子元素的出场效果**

需求场景：为ListView的 item 设置出场动画

**使用步骤：**

- 步骤1：设置子元素的出场动画

res/anim/view_animation.xml
```
<?xml version="1.0" encoding="utf-8"?> 
// 此处采用了组合动画 
<set xmlns:android="http://schemas.android.com/apk/res/android" > 
    android:duration="3000" 
    
    <alpha 
        android:duration="1500" 
        android:fromAlpha="1.0" 
        android:toAlpha="0.0" /> 
    
    <translate 
        android:fromXDelta="500" 
        android:toXDelta="0" /> 
        
</set>
```

- 步骤2：设置 视图组（ViewGroup）的动画文件

res/ anim /anim_layout.xml
```
<?xml version="1.0" encoding="utf-8"?> 
// 采用LayoutAnimation标签 
<layoutAnimation xmlns:android="http://schemas.android.com/apk/res/android" 

    android:delay="0.5" 
    // 子元素开始动画的时间延迟 
    // 如子元素入场动画的时间总长设置为300ms 
    // 那么 delay = "0.5" 表示每个子元素都会延迟150ms才会播放动画效果 
    // 第一个子元素延迟150ms播放入场效果；第二个延迟300ms，以此类推 
    
    android:animationOrder="normal" 
    // 表示子元素动画的顺序 
    // 可设置属性为： 
    // 1. normal ：顺序显示，即排在前面的子元素先播放入场动画 
    // 2. reverse：倒序显示，即排在后面的子元素先播放入场动画 
    // 3. random：随机播放入场动画 
    
    android:animation="@anim/view_animation" 
    // 设置入场的具体动画效果 
    // 将步骤1的子元素出场动画设置到这里 />
```

- 为视图组（ViewGroup）指定andorid:layoutAnimation属性

指定方式有两种： 

    -XML 
    -Java代码设置
    
在 XML 中指定
```
<?xml version="1.0" encoding="utf-8"?> 
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android" 
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent" 
    android:layout_height="match_parent" 
    android:background="#FFFFFF" 
    android:orientation="vertical" > 

    <ListView android:id="@+id/listView1"
        android:layoutAnimation="@anim/anim_layout" // 指定layoutAnimation属性用以指定子元素的入场动画
        android:layout_width="match_parent"
        android:layout_height="match_parent" /> 
        
</LinearLayout>
```

在Java代码中指定
```
ListView lv = (ListView) findViewById(R.id.listView1); 

// 加载子元素的出场动画 
Animation animation = AnimationUtils.loadAnimation(this,R.anim.anim_layout); 
LayoutAnimationController controller = new LayoutAnimationController(animation); 

// 设置LayoutAnimation的属性 
controller.setDelay(0.5f);
controller.setOrder(LayoutAnimationController.ORDER_NORMAL); 

// 为ListView设置LayoutAnimation的属性
lv.setLayoutAnimation(controller); 
```
