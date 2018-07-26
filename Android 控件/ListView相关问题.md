### ListView相关问题

**1. ScrollView嵌套ListVew，默认起始位置不是最顶部**

解决办法有两种
- 不让ListVew获取焦点 
```
listview.setFocusable(false);
```
> 注意：在xml里设置android：focusable="false"不生效

- ScrollView手动滑动到顶部
```
scrollView.smoothScrollTo(0, 0);
```

**2. Listview失去焦点**
 
在Listview子布局的根布局里添加如下属性，可以解决失去焦点的问题
```
android:descendantFocusability="blocksDescendants"
```

**3. Listview子布局里的Button，点击无响应**

原因：Button强制获取了item的焦点，只要设置button的focusable属性为false即可
```
android:focusable="false"
```

**4. Listview分页加载**
- 在ListView底部设置一个按钮，用户点击加载
- 当用户滑动到底部时自动加载

**自动加载实现思路**
- 实现OnScrollListener接口，重写onScrollStateChanged 和 onScroll方法
- 使用onscroll方法实现"滑动"后处理检查是否还有新的记录
    - 如果有，添加记录到adapter, adapter调用 notifyDataSetChanged 更新数据
    - 如果没有，则不再加载数据
- 使用onScrollStateChanged检测是否滚到最后一行且停止滚动，然后执行加载

**5. ListView图片异步加载实现思路**
- 先从内存缓存中获取图片显示（内存缓冲）
- 获取不到，则从SD卡里获取（SD卡缓冲：子线程里执行）
- 从网络下载图片 & 保存到SD卡，加入内存并显示（网络获取）

**6. ViewHolder声明成static**

----这不是Android的优化，而是Java提倡的优化

- 如果声明成员类不要求访问外围实例，就把static修饰符放在它的声明中，使它成为静态成员类
- 非静态成员类的实例包含一个额外的指向外围对象的引用，保存这份引用要消耗时间和空间，并且导致外围类实例符合垃圾回收时仍然被保留

**7. ListView 与 ScrollView兼容**
```
<ScrollView
    android:id="@+id/scrollview"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">
        
    <ListView
        android:id="@+id/listview"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
        
</ScrollView>
```

出现问题：Listview不能显示正常的条目，只显示一条或二条

----由于listView在scrollView中无法正确计算它的大小, 故只显示一行

**解决方案**

- 重写ListView, 覆盖onMeasure()方法
```
// 重写该方法，达到使ListView适应ScrollView的效果
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
    int expandSpec = MeasureSpec.makeMeasureSpec(Integer.MAX_VALUE >> 2, MeasureSpec.AT_MOST);
    super.onMeasure(widthMeasureSpec, expandSpec);
}
```

- 动态设置Listview的高度，不需要重写ListView
```
public void setListViewHeightBasedOnChildren(ListView listView) {
    // 获取ListView对应的Adapter
    ListAdapter listAdapter = listView.getAdapter();
    if (listAdapter == null) {
        return;
    }
    int totalHeight = 0;
    for (int i = 0, len = listAdapter.getCount(); i < len; i++) {
        // listAdapter.getCount()返回数据项的数目
        View listItem = listAdapter.getView(i, null, listView);
        // 计算子项View 的宽高
        listItem.measure(0, 0);
        // 统计所有子项的总高度
        totalHeight += listItem.getMeasuredHeight();
    }
    ViewGroup.LayoutParams params = listView.getLayoutParams();
    params.height = totalHeight + (listView.getDividerHeight() * (listAdapter.getCount() - 1));
    // listView.getDividerHeight()获取子项间分隔符占用的高度
    // params.height最后得到整个ListView完整显示需要的高度
    listView.setLayoutParams(params);
}
```

- 在xml文件中，直接将Listview的高度写死
- 避免ScrollView嵌套Listview，使用Listview的addHeader()来实现预期效果
- 使用LinearLayout代替Listview，通过ddView()遍历数据源