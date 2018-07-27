### RecyclerView

----RecyclerView 集成自 ViewGroup，是ListView的更高度定制版。

> RecyclerView --> ViewGroup --> View

> Android 5.0官方推出，support-v7包中的新组件。核心是：回收 & 重用

**使用场景**
- 高效展示大量数据
- 动态改变元素的列表

**RecyclerView 的组成**
- RecyclerView.LayoutManager：将每个条目的视图放置于适当的位置
- RecyclerView.Recyler
- RecyclerView.Adapter：包装数据集合 & 为每个条目创建视图
- RecyclerView.ViewHolder：保存用于显示每个数据条目的子View
- RecyclerView.ItemDecoration：在每个条目的视图的周围或上面绘制一些装饰视图
- RecyclerView.ItemAnimator：在条目被添加、移除或者重排序时添加动画效果

**RecyclerView**
- Adapter 继承 RecyclerView.Adapter
- ViewHolder是必须 & 强制的（RecyclerView.Adapter中的3个方法必须ViewHolder）
    - getItemCount()：总共显示多少条目
    - onCreateViewHolder()：创建ViewHolder
    - onBindViewHolder()：绑定数据
- setAdapter之前，一定要设置显示的样式，否则数据不能显示
    - ListView：new LinearLayoutManager(this, LinearLayoutManager.VERTICAL, false);
    - GridView：new GridLayoutManager(this, 3, OrientationHelper.VERTICAL, false);
    - 瀑布流：new StraggeredGridLayoutManager(this, 3, OrientationHelper.VERTICAL, false);
- 不支持直接在布局中添加分割线，要自定义分割线，通过addItemDecoration添加
- 不支持点击事件，只能用回调接口来设置
**在adapter中定义点击接口**
```
// 利用接口，给RecyclerView设置点击事件
public interface OnItemClickListener {

    void onItemClick(View view, int position);

    void onItemLongClick(View view, int position);
}
```

**在绑定数据的onBindViewHolder中给控件设置回调点击事件**
```
holder.itemView.setOnClickListener(new View.OnClickListener() {

    @Override
    public void onClick(final View v) {
        if(onItemClickListener != null) {
            int pos = holder.getLayoutPosition();
            onItemClickListener.onItemClick(holder.itemView, pos);
        }
    }
});

holder.itemView.setOnLongClickListener(new View.OnLongClickListener() {

    @Override
    public boolean onLongClick(View v) {
        if(onItemClickListener != null) {
            int pos = holder.getLayoutPosition();
            onItemClickListener.onItemLongClick(holder.itemView, pos);
        }
        return true;
    }
});
```

**优点**
- 封装ViewHolder的回收 & 复用
- 使用布局管理器管理子view的位置
     - StaggeredGridLayoutManager
     - GridLayoutManager 
     - LinearLayoutManager
- 自带ItemAnimation，可以设置加载和移除时的动画
- 自定义分割线ItemDecoration

**缺点**
- 没有提供空数据API
- 没有提供添加头和尾的API
- 没有提供设置子item的点击事件API
