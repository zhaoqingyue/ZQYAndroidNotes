### ListView

----复用是ListView最核心的原理

> ListView --> AbsListView --> AdapterView --> ViewGroup --> View

RecycleBin是AbsListView的内部类，也是复用的控制中心，正是RecycleBin的存在，让ListView有了复用的能力。

**ListView的两个职责**
- 将数据填充到布局
- 处理用户点击事件

**ListView的3个元素**
- ListView每一列的layout
- 填入View的数据Item
- 连接数据与ListView的适配器Adapter

**适配器**

----连接数据和ListView的桥梁，避免ListView和数据源直接接触

> ListView并不会直接和数据源打交道，而是借助Adapter这个桥梁去访问真正的数据源

Adapter | 描述
---|---
ArrayAdapter<T> | 绑定一个数组，支持泛型操作
SimpleAdapter | 绑定在xml中定义的控件对应的数据
SimpleCursorAdapter | 绑定游标得到的数据
BaseAdapter | 通用的基础适配器

**ListView的使用**
- ListView的 Adapter 继承 BaseAdapter
- 自定义 ViewHolder 和 convertView 完成复用优化工作
- 分割线在布局中设置 divider
- 点击事件是setOnItemClickListener

**常用属性**

**1. 分割线的高度和颜色设置**
- android:divider 设置颜色 & 背景
- android:dividerHeight 设置高度
```
android:divider="#FFFFFF"
android:dividerHeight="2dp"

// 设置无分割线
android:divider="@null"
```
> 两个属性必须同时使用，如果只设置divider，则没有效果，同时默认的分割线也会消失

> 也可以在item布局文件中添加分割线（在底部添加一个线）

**2. 滚动条**
```
// 隐藏滚动条
android:scrollbars="none"
```

**3. 去除滑到顶部和底部时边缘的黑色阴影**
```
android:fadingEdge="none"
```

**4. 去除滑动颜色**
```
android:cacheColorHint="@android:color/transparent"
```

**5. item点击反馈效果**
```
android:listSelector="#222222"

// 点击颜色设置为透明
android:listSelector="#0000"
```

**6. 设显示在第几项**
```
listView.setSelection(position);
```

**7. 平滑显示**
```
mListView.smoothScrollBy(distance, duiration);
mListView.smoothScrollByoffset(offset);
mListView.smoothScrollToPosition(position);
```


> GridView --> AbsListView --> AdapterView --> ViewGroup --> View

> Expandablelistview --> AbsListView --> AdapterView --> ViewGroup --> View

> ScrollView --> FrameLayout --> ViewGroup --> View