### ListView & RecyclerView

**ListView & RecyclerView的简单使用**

- ListView
1. 继承重写BaseAdapter类
2. 自定义ViewHolder与convertView的优化（判断是否为null）

- RecyclerView： 
1. 继承重写RecyclerView.Adapter与RecyclerView.ViewHolder 
2. 设置LayoutManager，以及layout的布局效果

**区别**

**1. 布局效果**
- ListView 的布局比较单一，只有一个纵向效果
- RecyclerView 的布局效果丰富，可以在LayoutMananger中设置
    - ListView：new LinearLayoutManager(this, LinearLayoutManager.VERTICAL, false);
    - GridView：new GridLayoutManager(this, 3, OrientationHelper.VERTICAL, false);
    - 瀑布流：new StraggeredGridLayoutManager(this, 3, OrientationHelper.VERTICAL, false);

**2. 空数据处理**
- ListView中有setEmptyView() 用来处理Adapter中数据为空的情况
- RecyclerView中没有空数据处理API（需要进行一些数据判断来实现数据为空的情况）

**3. 添加HeaderView & FooterView**
- ListView中可以通过addHeaderView() 与 addFooterView()来添加头部item与底部item
- RecyclerView中没有这两个API

> RecyclerView添加头部item或者底部item，可以在Adapter中自己编写，根据ViewHolder的Type与View来实现自己的Header，Footter与普通的item。但是会影响Adapter的数据，比如：添加了Header与Footter后，实际的position将大于数据的position。

**4. 局部刷新**
- ListView中用notifyDataSetChanged()刷新数据（全局刷新：每个item的数据都重新加载一遍，非常消耗资源）

> 如果要在ListView中实现局部刷新，是可以实现的：当一个item数据刷新时，可以在Adapter中，实现一个onItemChanged()方法，在方法里面获取到这个item的position（可以通过getFirstVisiblePosition()），然后调用getView()方法来刷新这个item的数据；

```
// 刷新单个item
public void onItemChanged(ListView listview, int itemIndex) {  
    // 得到第一个可显示控件的位置
    int visiblePosition = listview.getFirstVisiblePosition();  
    int headerCount = listview.getHeaderViewsCount();
    // 只有当要更新的view在可见的位置时才更新，不可见时，跳过不更新  
    int index = itemIndex - visiblePosition;
    if (index >= 0) {  
        View view = listview.getChildAt(index + headerCount);  
        ViewHolder holder = (ViewHolder) view.getTag();  
        holder.title = (RedDotTextView) view.findViewById(R.id.item_msg_sys_title);
        holder.title.setDotVisibility(View.INVISIBLE);
    }         
}  
```
- RecyclerView中可以实现局部刷新，例如：notifyItemChanged()

**5. Item点击事件**
- ListView提供的点击事件
    - onItemClickListener()
    - onItemLongClickListener()
    - onItemSelectedListener()
- RecyclerView提供的唯一API：addOnItemTouchListener()，监听item的触摸事件


**6. 动画效果**
- ListView中没有实现动画效果，但可以在Adapter中实现item的动画效果
- RecyclerView中，已经封装好API来实现自己的动画效果

----系统提供的默认动画：SimpleItemAnimator 和 DefaultItemAnimator

> 根据需要，实现自定义的动画效果，调用RecyclerView.setItemAnimator()

```
// 默认的动画效果
recyclerView.setItemAnimator(new DefaultItemAnimator());
```

**7. 嵌套滚动机制**
- ListView没有实现嵌套滚动机制
- RecyclerView实现嵌套滚动机制

> 嵌套滚动机制（Nested Scrolling：能让子View与父View同时处理Touch事件，主要实现在于NestedScrollingChild与NestedScrollingParent这两个接口

> 事件分发机制：Touch事件在进行分发时，由父View向子View传递，一旦子View消费这个事件，那么接下来的事件分发时，父View将不接受，由子View进行处理


**8. 缓存机制对比**

> 离屏的ItemView即被回收至缓存，入屏的ItemView则会优先从缓存中获取

- 层级不同：ListView(两级缓存) & RecyclerView(四级缓存)

**ListView(两级缓存)**
-- | 是否需要回调createView | 是否需要回调bindView | 声明周期 | 备注
---|---|---|---|---
mActiveViews | 否 | 否 | onLayout()周期内 | 用于屏幕内ItemView快速重用
mScrapViews | 否 | 是 | 与mAdapter一致，当mAdapter被更换时，mScrapViews即被清空 | --

**RecyclerView(四级缓存)**
-- | 是否需要回调createView | 是否需要回调bindView | 声明周期 | 备注
---|---|---|---|---
mAttachedScrap | 否 | 否 | onLayout()周期内 | 用于屏幕内ItemView快速重用
mCacheViews | 否 | 否 | 与mAdapter一致，当mAdapter被更换时，mCacheViews即被缓存至mRecyclerPool | 默认上限为2，即缓存屏幕外2个ItemView
mViewCacheExtension | -- | -- | -- | 不直接使用，需要用户定制，默认不实现
mRecyclerPool | 否 | 是 | 与自身生命周期一致，不再被引用时即被释放 | 默认上限为5，技术上可以实现所有RecyclerViewPool公用同一个

**9. 使用场景**
- 列表页展示界面，需要支持动画，或者频繁更新，局部刷新，建议使用RecyclerView
- 其它情况两者都OK，但ListView在使用上更加方便 & 快捷