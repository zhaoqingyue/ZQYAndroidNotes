### FloatingActionButton 

----悬浮操作按钮，它继承自ImageView，具备有ImageView所有的方法和属性。

> FloatingActionButton默认是圆形

**常用属性：**

- src：设置FAB的图标（图标大小为24dp）
- backgroundTint：设置FAB的背景颜色
- rippleColor：设置FAB点击时的背景颜色 
- borderWidth 
- elevation：默认状态下FAB的阴影大小
- pressedTranslationZ：点击时候FAB的阴影大小 
- fabSize：设置FAB的大小
- layout_anchor：设置FAB的锚点，即以哪个控件为参照点设置位置
- layout_anchorGravity：设置FAB相对锚点的位置，值有 bottom、center、right、left、top等
```
<android.support.design.widget.FloatingActionButton
    android:id="@+id/fab"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_alignParentBottom="true"
    android:layout_alignParentRight="true"
    android:layout_margin="16dp"
    android:src="@drawable/ic_add_black_24dp"

    app:backgroundTint="@color/colorFabNor"
    app:rippleColor="@color/colorFabPress"
    app:borderWidth="0dp"
    app:elevation="0dp"
    app:pressedTranslationZ="0dp"
    app:fabSize="normal" />
```

遇到的问题
```
Error inflating class android.support.design.widget.FloatingActionButton
```
解决方案：
```
android:backgroundTint="@color/colorFabNor"

改成

app:backgroundTint="@color/colorFabNor"
```

