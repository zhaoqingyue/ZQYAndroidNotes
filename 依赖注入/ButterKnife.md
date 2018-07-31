### Android ButterKnife（黄油刀）

----专注于Android系统的View注入框架

> 作者：JakeWharton

ButterKnife项目地址：https://github.com/JakeWharton/butterknife

**ButterKnife的优势**
- 强大的View绑定和Click事件处理功能，简化代码，提升开发效率
- 方便的处理Adapter里的ViewHolder绑定问题
- 运行时不会影响APP效率，使用配置方便
- 代码清晰，可读性强

**1. 在app/build.gradle添加依赖** 
```
// 依赖注入
compile 'com.jakewharton:butterknife:8.8.1'
annotationProcessor 'com.jakewharton:butterknife-compiler:8.8.1'
```

**2. 注册 & 绑定**

**2.1 在Activity中绑定ButterKnife**
```
public class MainActivity extends AppCompatActivity {  

    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
        
        //绑定初始化ButterKnife  
        ButterKnife.bind(this);  
    }  
}  
```
**使用说明**
- 在onCreate绑定Activity，建议写一个BaseActivity完成绑定，子类继承即可
- 绑定必须在setContentView之后

**2.2 在Fragment中绑定ButterKnife**
```
public class MainFragment extends Fragment {  
    private Unbinder unbinder;  
    
    @Override  
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {  
        View view = inflater.inflate(R.layout.fragment, container, false);  
        
        //返回一个Unbinder值
        unbinder = ButterKnife.bind(this, view);  
        return view;  
    }  

   
    @Override  
    public void onDestroyView() {  
        super.onDestroyView();  
        
        // onDestroyView中进行解绑操作 
        unbinder.unbind();  
    }  
}  
```
**使用说明**
- ButterKnife.bind(this, view);中的this不能使用getActivity()
- 在onDestroyView()回调中调用unbind()方法进行Fragment解绑

**2.3 在Adapter中绑定ButterKnife**
```
public class MyAdapter extends BaseAdapter {  

    @Override   
    public View getView(int position, View view, ViewGroup parent) {  
        ViewHolder holder;  
        if (view != null) {  
            holder = (ViewHolder) view.getTag();  
        } else {  
            view = inflater.inflate(R.layout.item, parent, false);  
            holder = new ViewHolder(view);  
            view.setTag(holder);  
        }  

        holder.name.setText("Zhaoqy");  
        holder.job.setText("Android");
        return view;  
    }  

    static class ViewHolder {  
        @BindView(R.id.title) 
        TextView name;  
        
        @BindView(R.id.job) 
        TextView job;  

        public ViewHolder(View view) {  
            ButterKnife.bind(this, view);  
        }  
    }  
}  
```
**使用说明**
- 在Adapter的ViewHolder中使用
- 写一个ViewHolder构造方法，在new ViewHolder的时候把view传递进去

**3. 基本使用**

----不能用private or static修饰

**3.1 绑定View**

- @BindView()：控件id注解 
```
@BindView(R.id.button)  
Button button;   
```

- @BindViews()：多个控件id注解
```
@BindViews({R.id.button1, R.id.button2,  R.id.button3})  
List<Button> buttonList ; 

....

buttonList.get(0).setText("button1");  
buttonList.get(1).setText("button2");  
buttonList.get(2).setText("button3");  
```

**3.2 绑定资源**

- @BindString()：绑定string字符串
```
// 绑定资源文件中string字符串  
@BindString(R2.string.app_name)  
String str;  

...

button.setText(str);  
```

- @BindArray()：绑定string里面array数组
```
<resources>  
    <string-array name="city">  
        <item>北京</item>  
        <item>天津</item>  
        <item>上海</item>  
        <item>重庆</item>  
    </string-array>  
</resources>  

----------

// 绑定string里面array数组  
@BindArray(R.array.city)  
String [] citys; 

...

button.setText(citys[0]);  
```
- @BindBool：绑定boolean值
- @BindInt：绑定int
- @BindFloat：绑定float
- @BindBitmap()：绑定Bitmap资源
```
@BindView(R.id.imageView) 
ImageView imageView ;  

// 绑定Bitmap资源  
@BindBitmap(R.mipmap.bm)
Bitmap bitmap;  

...

imageView.setImageBitmap(bitmap);  
```
- @BindDrawable：绑定Drawable
```
@BindDrawable(R.drawable.test_pic) 
Drawable mTestPic;
```

- @BindColor()：绑定颜色值
```
@BindColor(R.color.colorAccent) 
int black;  

...

button.setTextColor(black);  
```

- @BindDimen：绑定Dimen
```
@BindDimen(R.dimen.borth_width) 
int mBorderWidth;
```

**3.3 事件绑定**

- @OnClick() & @OnLongClick()：绑定点击事件
```
// 给button1设置点击事件  
@OnClick(R.id.button1)   
public void showToast() {  
    Toast.makeText(this, "is a click", Toast.LENGTH_SHORT).show();  
}  

// 给button1设置长按事件：返回true，可以拦截onItemClick
@OnLongClick(R.id.button1)    
public boolean showToast2() {  
    Toast.makeText(this, "is a long click", Toast.LENGTH_SHORT).show();  
    return true ;  
}  
```

- 指定多个id绑定事件
```
@OnClick({R.id.button1, R.id.button2, R.id.button3})  
public void OnClick(View view) {  
    switch (view.getId()) {  
        case R.id.button1:  
            System.out.print("点击事件1");  
            break;  
        case R.id.button2:  
            System.out.print("点击事件2");  
            break;  
        case R.id.button3:  
            System.out.print("点击事件3");  
            break;  
    }  
}  
```

- @OnCheckedChanged监听
```
@OnCheckedChanged({R.id.rg1, R.id.rg2, R.id.rg3})  
public void OnCheckedChangeListener(CompoundButton view, boolean ischanged ){  
    switch (view.getId()) {  
        case R.id.rg1:  
            if (ischanged) {
                // 按钮变化状态的UI及相关逻辑  
            }  
            break;  
        case R.id.rg2:  
            if (ischanged) {  
                // 按钮变化状态的UI及相关逻辑  
            }  
            break;  
        case R.id.rg3:  
            if (ischanged) {  
                // 按钮变化状态的UI及相关逻辑  
            }  
            break;  
        }  
    }  
```

- @OnEditorAction：软键盘的功能键
- @OnFocusChange：焦点改变
- @OnItemClick：Item被点击(如果item里面有Button，设置属性focusable为false)
- @OnItemLongClick：item长按(返回真可以拦截onItemClick)
- @OnItemSelected：item被选择事件
- @OnPageChange：页面改变事件
- @OnTextChanged：EditText里面的文本变化事件
- @OnTouch：触摸事件

**4. 代码混淆**

在混淆文件中，添加如下代码

```
-keep class butterknife.** { *; }  
-dontwarn butterknife.internal.**  
-keep class **$$ViewBinder { *; }  

-keepclasseswithmembernames class * {  
    @butterknife.* <fields>;  
}  

-keepclasseswithmembernames class * {  
    @butterknife.* <methods>;  
}  
```
