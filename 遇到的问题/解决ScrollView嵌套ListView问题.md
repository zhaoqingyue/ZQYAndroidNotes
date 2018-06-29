### 解决ScrollView嵌套ListView问题

----不光是ListView，其他继承自AbsListView的也适用，包括ExpandableListView、GridView等等，为了方便说明，以下均用ListView来代表。

**引言：**

----ScrollView中只能放一个控件，一般都放LinearLayout，orientation属性值为vertical。在LinearLayout中放需要呈现的内容。

**原因：**

----scroll事件的消费处理以及ListView控件的高度设定问题。

**解决方案：**

----自定义可适应ScrollView的ListView（通过重写其onMeasure方法，达到对ScrollView适配的效果）。

1. 自定义ListView

```
public class ListViewForScrollView extends ListView {

    public ListViewForScrollView(Context context) {
        super(context);
    }

    public ListViewForScrollView(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public ListViewForScrollView(Context context, AttributeSet attrs, int defStyle) {
        super(context, attrs, defStyle);
    }

    /**
     * 重写该方法，达到使ListView适应ScrollView的效果
     */
    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        int expandSpec = MeasureSpec.makeMeasureSpec(Integer.MAX_VALUE >> 2, MeasureSpec.AT_MOST);
        super.onMeasure(widthMeasureSpec, expandSpec);
    }
}
```

三个构造方法完全不用动，只要重写onMeasure方法。

2. 在xml布局种使用自定义的ListView

```
<ScrollView
    android:id="@+id/sv"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <TextView
            android:id="@+id/tv_des"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:padding="5dp"
            android:lineSpacingExtra="5dp"
            android:text=""/>

        <com.mtq.zqydatastorage.ui.widget.ListViewForScrollView
            android:id="@+id/listView"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:divider="@null"
            android:paddingBottom="0dp"
            android:paddingTop="10dp"
            android:background="@color/colorWindows"/>

    </LinearLayout>

</ScrollView>
```

3. 在Activity中使用自定义的ListView

```
@BindView(R.id.listView)
ListViewForScrollView listView;

ApiAdapter adapter;

adapter = new ApiAdapter(this, FileApiHelper.getExternalApiList());
listView.setAdapter(adapter);

```

4. 手动把ScrollView滚动至最顶端

```
@BindView(R.id.sv)
ScrollView sv;

sv.smoothScrollTo(0, 0);
```
