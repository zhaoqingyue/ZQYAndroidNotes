### Fragment的使用

**1. 创建Fragment**
 
**1.1 静态加载Fragment**

- 步骤1：定义Fragment的xml布局文件
- 步骤2：自定义Fragment类，继承Fragment或其子类
    - 重写onCreateView()方法
    - 在该方法中调用Inflater.inflate()方法加载Fragment的布局文件
    - 返回加载的view对象
```
public class MainFragment extends Fragment {

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
            Bundle savedInstanceState) {
        View view = Inflater.inflate(R.layout.fragment_main, container,false);
        return view;
    }   
}
```

- 步骤3：在需要加载Fragment的Activity对应的xml布局文件中添加fragment标签
```
<fragment
    android:id="@+id/fragment"
    android:name="com.zqy.test.MainFragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

- 步骤4：在Activity的onCreate()方法中调用setContentView()加载布局文件

**1.2 动态加载Fragment**

- 步骤1：获取FragmentManager对象，通过getFragmentManager() 
- 步骤2：获取FragmentTransaction对象fm.beginTransaction()
- 步骤3：调用add()或replace()方法加载Fragment
- 步骤4：调用commit()方法提交事务
```
MainFragment fragment = new MainFragment();
getFragmentManager().beginTransaction().replace(R.id.LinearLayout, fragment).commit();
```

**3. Fragment & Activity 交互**

**3.1 组件获取**

- Fragment获得Activity中的组件
```
getActivity().findViewById(R.id.list)；
```
- Activity获得Fragment中的组件
```
getFragmentManager.findFragmentByid(R.id.fragment);
```
**3.2 数据传递**

- Activit传递数据给Fragment
    - 在Activity中创建Bundle
    - 调用Fragment实例的setArguments(bundle)，将Bundle数据包传给Fragment
    - Fragment中调用getArguments获得Bundle对象
    - 解析Bundle数据包

- Fragment传递数据给Activity
    - 在Fragment中定义一个内部回调接口
    - 包含该Fragment的Activity实现该回调接口

**使用步骤**
- 步骤1：定义一个回调接口（Fragment中）
```
public interface CallBack {  

    // 定义一个获取信息的方法
    public void getResult(String result);  
} 
```
- 步骤2：接口回调（Fragment中）
```
public void getData(CallBack callBack) {  

    // 获取文本框的信息
    String msg = editText.getText().toString();  
    callBack.getResult(msg);  
}  
```
- 步骤3：使用接口回调方法读数据（Activity中）
```
fragment.getData(new CallBack() {  

    @Override  
    public void getResult(String result) {              
        ...  
    }
}); 
```
