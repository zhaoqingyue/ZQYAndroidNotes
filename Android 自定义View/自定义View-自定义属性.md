### 自定义View-自定义属性

**1. 自定义属性类型**

**1.1 reference：使用某一资源ID**
```
<declare-styleable name="名称">
    <attr name="background" format="reference" />
</declare-styleable>

// 格式使用
<ImageView
    android:layout_width="42dp"
    android:layout_height="42dp"
    android:background="@drawable/图片ID" />
```

**1.2 color：颜色值**
```
<declare-styleable name="名称">
    <attr name="textColor" format="color" />
</declare-styleable>

// 格式使用
<TextView
    android:layout_width="42dp"
    android:layout_height="42dp"
    android:textColor="#00FF00" />
```

**1.3 boolean：布尔值**
```
<declare-styleable name="名称">
    <attr name="focusable" format="boolean" />
</declare-styleable>

// 格式使用
<Button
    android:layout_width="42dp"
    android:layout_height="42dp"
    android:focusable="true" />
```

**1.4 dimension：尺寸值**
```
<declare-styleable name="名称">
    <attr name="layout_width" format="dimension" />
</declare-styleable>

// 格式使用
<Button
    android:layout_width="42dp"
    android:layout_height="42dp" />
```

**1.5 float：浮点值**
```
<declare-styleable name="名称">
    <attr name="fromAlpha" format="float" />
    <attr name="toAlpha" format="float" />
</declare-styleable>

// 格式使用
<alpha
    android:fromAlpha="1.0"
    android:toAlpha="0.7" />
```

**1.6 integer：整型值**
```
<declare-styleable name="名称">
    <attr name="frameDuration" format="integer" />
    <attr name="framesCount" format="integer" />
</declare-styleable>

// 格式使用
<animated-rotate
    android:frameDuration="100"
    android:framesCount="12" />
```

**1.7 string：字符串**
```
<declare-styleable name="名称">
    <attr name="apiKey" format="string" />
</declare-styleable>

// 格式使用
<com.google.android.maps.MapView
    android:apiKey="0jOkQ80o" />
```

**1.8 fraction：百分数**
```
<declare-styleable name="名称">
    <attr name="pivotX" format="fraction" />
    <attr name="pivotY" format="fraction" />
</declare-styleable>

// 格式使用
<rotate
    android:pivotX="200%"
    android:pivotY="300%" />
```

**1.9 enum：枚举值**
```
<declare-styleable name="名称">
    <attr name="orientation">
        <enum name="horizontal" value="0" />
        <enum name="vertical" value="1" />
    </attr>
</declare-styleable>

// 格式使用
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" />
```

**2. 自定义属性步骤**

- 在values目录下创建自定义属性的xml文件
- 在自定义View的构造方法中解析自定义属性的值
- 在布局文件中使用自定义属性

**2.1 在values目录下创建自定义属性的xml文件**
```
<?xml version="1.0" encoding="utf-8"?>
<resources>

    <!--自定义属性集合:CircleView-->
    <declare-styleable name="CircleView">
        <!--在attr标签下设置需要的自定义属性-->
        <attr name="circle_color" format="color"/>
    </declare-styleable>
    
</resources>
```

**2.2 在自定义View的构造方法中解析自定义属性的值**
```
public CircleView(Context context, AttributeSet attrs) {
    this(context, attrs,0);
    init();
}

public CircleView(Context context, AttributeSet attrs, int defStyleAttr) {
    super(context, attrs, defStyleAttr);

    // 加载自定义属性集合CircleView
    TypedArray ta = context.obtainStyledAttributes(attrs,R.styleable.CircleView);

    // 解析集合中的属性circle_color属性
    // 该属性的id为: R.styleable.CircleView_circle_color
    // 第二个参数是默认设置颜色
    mColor = ta.getColor(R.styleable.CircleView_circle_color, Color.RED);

    // 解析后释放资源
    ta.recycle();

    init();
}
```

**2.3 在布局文件中使用自定义属性**
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    <!--必须添加schemas声明才能使用自定义属性-->
    xmlns:app="http://schemas.android.com/apk/res-auto"
    
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity" >
  
    <!-- 注意添加自定义View组件的标签名：包名 + 自定义View类名 -->
    <com.mtq.customview.CircleView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="#000000"
        android:padding="30dp"

        <!--设置自定义颜色-->
        app:circle_color="#FF4081" />
        
</RelativeLayout>
```
