### ListView优化

- ListView的宽和高设置为match_parent，减少测量次数
```
android:layout_width="match_parent"
android:layout_height="match_parent"
```

- 复用convertView（**ListView的核心原理就是重用View**）

> 对convetView进行判空，当convertView不为空时重复使用；为空，则初始化，从而减少View的创建

- 采用ViewHolder模式，缓存item条目的引用 & 减少findViewById()的次数

> ViewHolder，保存item中子控件的引用。对于同一个converView布局，其子控件的引用是不变的，可以获取converView上的所有子控件的引用并通过ViewHolder进行保存

- 减少Item的布局层级
- 避免在getView()中做耗时操作
- 避免在getView()中创建大量对象
- 分页加载数据
- 滑动时，不要加载图片
- 异步加载图片，并对图片加载做优化
- 将ListView的scrollingCache和animateCache设置为false
