### RecyclerView的使用

**1. 在app/build.gradle中添加依赖**
```
compile 'com.android.support:recyclerview-v7:25.3.1'
```

**2. 在xml中写RecyclerView的布局**
```
<android.support.v7.widget.RecyclerView
    android:id="@+id/recyclerView"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:scrollbars="vertical"/>
```

**3. 编写item的布局文件**
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="@dimen/vertical_card_55"
    android:background="@drawable/drawer_item_selector"
    android:orientation="vertical">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:layout_marginLeft="20dp"
        android:layout_marginRight="20dp"
        android:gravity="center_vertical">

        <TextView
            android:id="@+id/letter"
            android:layout_width="30dp"
            android:layout_height="30dp"
            android:layout_marginRight="20dp"
            android:background="@drawable/shape_circle_bg"
            android:gravity="center"
            android:text=""
            android:textColor="@color/colorCircle"
            android:textSize="16sp"/>

        <TextView
            android:id="@+id/title"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text=""
            android:textColor="@color/colorNormal"
            android:textSize="@dimen/textSize_16"/>

    </LinearLayout>

</LinearLayout>
```

**4. 编写连接数据源 & RecyclerView的适配器Adapter**
```
public class BookAdapter extends RecyclerView.Adapter<BookAdapter.ViewHolder>{

    /**
     * 展示数据
     */
    private List<String> mData;

    /**
     * 事件回调监听
     */
    private OnItemClickListener onItemClickListener;

    public BookAdapter(List<String> data) {
        this.mData = data;
    }

    public void updateData(List<String> data) {
        this.mData = data;
        notifyDataSetChanged();
    }

    /**
     * 添加新的Item
     */
    public void addNewItem() {
        if(mData == null) {
            mData = new ArrayList<>();
        }
        mData.add(0, "new Item");
        notifyItemInserted(0);
    }

    /**
     * 删除Item
     */
    public void deleteItem() {
        if(mData == null || mData.isEmpty()) {
            return;
        }
        mData.remove(0);
        notifyItemRemoved(0);
    }

    /**
     * 设置回调监听
     *
     * @param listener
     */
    public void setOnItemClickListener(OnItemClickListener listener) {
        this.onItemClickListener = listener;
    }

    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        /**
         * 实例化展示的view
         */
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_book, parent, false);
        /**
         * 实例化viewholder
         */
        ViewHolder viewHolder = new ViewHolder(view);
        return viewHolder;
    }

    @Override
    public void onBindViewHolder(final ViewHolder holder, int position) {
        /**
         * 绑定数据
         */
        String string = mData.get(position);
        holder.letter.setText(string.subSequence(0, 1));
        holder.title.setText(string);
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
                /**
                 * 此事件已经消费，不会触发单击事件
                 */
                return true;
            }
        });
    }

    @Override
    public int getItemCount() {
        return mData == null ? 0 : mData.size();
    }

    public static class ViewHolder extends RecyclerView.ViewHolder {

        TextView letter;
        TextView title;

        public ViewHolder(View itemView) {
            super(itemView);
            letter = (TextView) itemView.findViewById(R.id.letter);
            title = (TextView) itemView.findViewById(R.id.title);
        }
    }

    public interface OnItemClickListener {

        void onItemClick(View view, int position);

        void onItemLongClick(View view, int position);
    }
}
```

**3个重要的方法**
- onCreateViewHolder
- onBindViewHolder
- getItemCount()

**自定义编写ViewHolder**
```
public static class ViewHolder extends RecyclerView.ViewHolder {

    TextView letter;
    TextView title;

    public ViewHolder(View itemView) {
        super(itemView);
        letter = (TextView) itemView.findViewById(R.id.letter);
        title = (TextView) itemView.findViewById(R.id.title);
    }
}
```

**自定义监听事件**
```
public interface OnItemClickListener {

    void onItemClick(View view, int position);

    void onItemLongClick(View view, int position);
}
```

**5. 在Activity初始化RecycleView**
```
@BindView(R.id.recyclerView)
RecyclerView recyclerView;

private BookAdapter mAdapter;
private RecyclerView.LayoutManager mLayoutManager;
private List<String> mDatas;

@Override
protected int getLayoutResID() {
    return R.layout.activity_recycler_view;
}

@Override
protected void initData() {
    mLayoutManager = new LinearLayoutManager(this, LinearLayoutManager.VERTICAL, false);
    mDatas = new ArrayList<>();
    mDatas.addAll(Arrays.asList(getResources().getStringArray(R.array.recyclerview)));
    mAdapter = new BookAdapter(mDatas);
    mAdapter.setOnItemClickListener(this);
    recyclerView.setLayoutManager(mLayoutManager);
    recyclerView.setAdapter(mAdapter);
    recyclerView.setItemAnimator(new DefaultItemAnimator());
    recyclerView.addItemDecoration(new LinearDivider(this, LinearLayoutManager.VERTICAL));
}

@Override
public void onItemClick(View view, int position) {
        
}

@Override
public void onItemLongClick(View view, int position) {
    Toast.makeText(this,"long click " + position + " item", Toast.LENGTH_SHORT).show();
}
```

**自定义分割线**
```
public class LinearDivider extends RecyclerView.ItemDecoration {
    ...
}
```
