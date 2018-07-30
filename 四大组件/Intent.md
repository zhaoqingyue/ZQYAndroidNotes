### Android Intent

----Android四大组件间的枢纽——Intent(意图)，Android通信的桥梁。

例如：
- startActivity(Intent) / startActivityForResult(Intent)：启动一个Activity
- startService(Intent) / bindService(Intent)：启动一个Service
- sendBroadcast(Intent)：发送广播到指定BroadcastReceiver

**1. 显式Intent与隐式Intent**

- 显式Intent：通过组件名指定启动的目标组件
```
Intent intent = new Intent(A.this, B.class);
// 每次启动的组件只有一个
startActivity(intent); 
```

- 隐式Intent：不指定组件名，而指定Intent的Action、Data或Category。

----当启动组件时，会去匹配AndroidManifest.xml相关组件的Intent-filter，逐一匹配出满足属性的组件。当不止一个满足时，会弹出一个选择启动哪个的对话框。
```
Intent intent = new Intent();
intent.setAction("xxx_action");
intent.addCategory("xxx_category");
startActivity(intent);   
```

**2. Intent的7个属性**

**2.1 ComponentName (组件名称)**

----目标组件的名称。由组件所在的应用程序配置文件中设置的包名 + 组件的全限定类名组成。这是显示的Intent，启动的组件只有一个。

设置方法：
- setClass()
- setClassName()
- setComponent()

获取方法：
- getComponent()

```
ComponentName cn = new ComponentName(ClassA.this, ClassB.class);
Intent intent = new Intent();
intent.setClassName();

// 也可以直接写成
Intent intent = new Intent(ClassA.this, ClassB.class);

// 两个参数：
// 第一个是Context：上下文，其实就是包名
// 第二个是要启动的Activity的类名
// 有此就确定了：包名 + 组件 = 全限定类名
```

**2.2 Action (动作)**

----一个普通的字符串，代表Intent要完成的一个抽象的"动作"，比如发送信息的权限。具体由哪个组件来完成，Intent并不负责。仅仅知道会有这个动作，谁来完成就交给intent-filter来进行筛选。

**注意：**

----在java中的Action与intent-filter中的格式时不一样的。

```
// AndroidManifest.xml
<intent-filter>
    <action android:name="android.intent.action.MAIN"/>
</intent-filter>

// Java代码
Intent intent = new Intent();
intent.setAction(Intent.ACTION_MAIN);
```

**2.3 Category (类别)**

----同样是普通的字符串，Category则用于为Action提供额外的附加类别信息。两者通常结合使用，一个Intent对象只能有一个Action，但是能有多个Category。

**注意：**

----在java中的Category与intent-filter中的格式时不一样的。

```
// AndroidManifest.xml
<intent-filter>
    <category android:name="android.intent.category.LAUNCHER"/>
</intent-filter>

// Java代码
Intent intent = new Intent();
intent.addCategory(Intent.CATEGORY_DEFAULT);

// 可以调用removeCategory()删除上次添加的类别
// 也可以调用getCategories()获取当前对象所包含的全部类别
```

**2.4 Data (数据)**

----Data通常用于向Action属性提供操作的数据，接收一个URI对象。

```
// URI格式：
scheme://host:port/path
// 参数依次为：协议头 + 主机 + 端口 + 路径
```

如果在Java代码中进行设置，这两个属性时会相互覆盖的。如果需要两个属性都有的话，调用setDataAndType()方法进行设置。

在AndroidManifest.xml文件中，这两个属性都是放在data标签中的：

**2.5 Type (MIME类型)**

----Type通常用于指定Data所指定的URI对应的MIME类型。比如能够显示图片数据的组件不应该应用来播放音频文件，可以是自定义的MIME类型，只要符合abc/xyz格式的字符串就可以。

**2.6 Extras (额外)**

----通常用于多个Action之间的数据交换，Extra属性时一个Bundle对象，通过键值对惊醒数据的存储。

```
intent.putExtra()
intent.getExtras()   
```

**2.7 Flags (标记)**

----表述不同来源的标记，多用于只是Android如何启动Activity以及启动后如何对待。标记定义在Intent中，比如：Intent.FLAG_ACTIVITY_SINGLE_TOP，相当于启动模式为singleTop模式。




**3 Intent数据传递**

**3.1 Intent传递基本数据类型**

- 传递一个数据

```
// 保存数据（在Activity A中）
Intent intent = new Intent(A.this, B.class);
intent.putExtra("key", "value");
startActivity(intent);

// 取数据（在Activity B中）
Intent intent = getIntent();
String value = intent.getStringExtra("key");
```

- 传递多个数据

```
// 保存数据（在Activity A中）
Intent intent = new Intent(A.this, B.class);
Bundler bundler = new Bundler();
bundler.putInt("key0", value0);
bundler.putString("key1", "value1");
intent.putExtra(bundler);
startActivity(intent);

// 取数据（在Activity B中）
Intent intent = getIntent();
Bundler bundler = intent.getExtra();
int value1 = bundler.getInt("key1");
String value2 = bundler.getString("key2");
```

**3.2 Intent传递数组**

```
// 写入数组（可把StringArray换成其他数据类型,比如int,float等）
Bundler bundler = new Bundler();
bundler.putStringArray("StringArray", new String[]{"str0","str1"});

// 读取数组
Bundler bundler = intent.getExtra();
String[] str = bundler.getStringArray("StringArray")
```



**3.3 Intent传递集合**

**3.4 Intent传递对象**

**3.5 Intent传递Bitmap**

----Bitmap默认实现Parcelable接口，直接传递即可。
```
Bitmap bitmap = null;
Intent intent = new Intent();
Bundle bundle = new Bundle();
bundle.putParcelable("bitmap", bitmap);
intent.putExtra("bundle", bundle);
```