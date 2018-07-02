### Android Intent

----Android四大组件间的枢纽——Intent(意图)，Android通信的桥梁。

例如：
- startActivity(Intent) / startActivityForResult(Intent)：启动一个Activity
- startService(Intent) / bindService(Intent)：启动一个Service
- sendBroadcast(Intent)：发送广播到指定BroadcastReceiver

**1. 显式Intent与隐式Intent**

- 显式Intent：通过组件名指定启动的目标组件

例如：

```
Intent intent = new Intent(A.this,B.class);
// 每次启动的组件只有一个
startActivity(intent); 
```

- 隐式Intent：不指定组件名，而指定Intent的Action、Data或Category。

----当启动组件时，会去匹配AndroidManifest.xml相关组件的Intent-filter，逐一匹配出满足属性的组件。当不止一个满足时，会弹出一个选择启动哪个的对话框。

例如：

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

**4. 常用系统Intent**

```
// 1. 拨打电话（给移动客服10086拨打电话）
Uri uri = Uri.parse("tel:10086");
Intent intent = new Intent(Intent.ACTION_DIAL, uri);
startActivity(intent);

//===============================================================

// 2. 发送短信（给10086发送内容为“Hello”的短信）
Uri uri = Uri.parse("smsto:10086");
Intent intent = new Intent(Intent.ACTION_SENDTO, uri);
intent.putExtra("sms_body", "Hello");
startActivity(intent);

//===============================================================

// 3. 发送彩信（相当于发送带附件的短信）
Intent intent = new Intent(Intent.ACTION_SEND);
intent.putExtra("sms_body", "Hello");
Uri uri = Uri.parse("content://media/external/images/media/23");
intent.putExtra(Intent.EXTRA_STREAM, uri);
intent.setType("image/png");
startActivity(intent);

//===============================================================

// 4. 打开浏览器（打开百度主页）
Uri uri = Uri.parse("http://www.baidu.com");
Intent intent  = new Intent(Intent.ACTION_VIEW, uri);
startActivity(intent);

//===============================================================

// 5. 发送电子邮件（给someone@domain.com发邮件）
Uri uri = Uri.parse("mailto:someone@domain.com");
Intent intent = new Intent(Intent.ACTION_SENDTO, uri);
startActivity(intent);
// 给someone@domain.com发邮件发送内容为“Hello”的邮件
Intent intent = new Intent(Intent.ACTION_SEND);
intent.putExtra(Intent.EXTRA_EMAIL, "someone@domain.com");
intent.putExtra(Intent.EXTRA_SUBJECT, "Subject");
intent.putExtra(Intent.EXTRA_TEXT, "Hello");
intent.setType("text/plain");
startActivity(intent);
// 给多人发邮件
Intent intent=new Intent(Intent.ACTION_SEND);
String[] tos = {"1@abc.com", "2@abc.com"}; // 收件人
String[] ccs = {"3@abc.com", "4@abc.com"}; // 抄送
String[] bccs = {"5@abc.com", "6@abc.com"}; // 密送
intent.putExtra(Intent.EXTRA_EMAIL, tos);
intent.putExtra(Intent.EXTRA_CC, ccs);
intent.putExtra(Intent.EXTRA_BCC, bccs);
intent.putExtra(Intent.EXTRA_SUBJECT, "Subject");
intent.putExtra(Intent.EXTRA_TEXT, "Hello");
intent.setType("message/rfc822");
startActivity(intent);

//===============================================================

// 6. 显示地图:
// 打开Google地图中国北京位置（北纬39.9，东经116.3）
Uri uri = Uri.parse("geo:39.9,116.3");
Intent intent = new Intent(Intent.ACTION_VIEW, uri);
startActivity(intent);

//===============================================================

// 7. 路径规划
// 路径规划：从北京某地（北纬39.9，东经116.3）到上海某地（北纬31.2，东经121.4）
Uri uri = Uri.parse("http://maps.google.com/maps?f=d&saddr=39.9 116.3&daddr=31.2 121.4");
Intent intent = new Intent(Intent.ACTION_VIEW, uri);
startActivity(intent);

//===============================================================

// 8. 多媒体播放:
Intent intent = new Intent(Intent.ACTION_VIEW);
Uri uri = Uri.parse("file:///sdcard/foo.mp3");
intent.setDataAndType(uri, "audio/mp3");
startActivity(intent);

//获取SD卡下所有音频文件,然后播放第一首=-= 
Uri uri = Uri.withAppendedPath(MediaStore.Audio.Media.INTERNAL_CONTENT_URI, "1");
Intent intent = new Intent(Intent.ACTION_VIEW, uri);
startActivity(intent);

//===============================================================

// 9. 打开摄像头拍照:
// 打开拍照程序
Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE); 
startActivityForResult(intent, 0);
// 取出照片数据
Bundle extras = intent.getExtras(); 
Bitmap bitmap = (Bitmap) extras.get("data");

// 另一种:
// 调用系统相机应用程序，并存储拍下来的照片
Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE); 
time = Calendar.getInstance().getTimeInMillis();
intent.putExtra(MediaStore.EXTRA_OUTPUT, Uri.fromFile(new File(Environment
.getExternalStorageDirectory().getAbsolutePath()+"/tucue", time + ".jpg")));
startActivityForResult(intent, ACTIVITY_GET_CAMERA_IMAGE);

//===============================================================

// 10. 获取并剪切图片
// 获取并剪切图片
Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
intent.setType("image/*");
intent.putExtra("crop", "true"); // 开启剪切
intent.putExtra("aspectX", 1); // 剪切的宽高比为1：2
intent.putExtra("aspectY", 2);
intent.putExtra("outputX", 20); // 保存图片的宽和高
intent.putExtra("outputY", 40); 
intent.putExtra("output", Uri.fromFile(new File("/mnt/sdcard/temp"))); // 保存路径
intent.putExtra("outputFormat", "JPEG");// 返回格式
startActivityForResult(intent, 0);
// 剪切特定图片
Intent intent = new Intent("com.android.camera.action.CROP"); 
intent.setClassName("com.android.camera", "com.android.camera.CropImage"); 
intent.setData(Uri.fromFile(new File("/mnt/sdcard/temp"))); 
intent.putExtra("outputX", 1); // 剪切的宽高比为1：2
intent.putExtra("outputY", 2);
intent.putExtra("aspectX", 20); // 保存图片的宽和高
intent.putExtra("aspectY", 40);
intent.putExtra("scale", true);
intent.putExtra("noFaceDetection", true); 
intent.putExtra("output", Uri.parse("file:///mnt/sdcard/temp")); 
startActivityForResult(intent, 0);

//===============================================================

// 11. 打开Google Market 
// 打开Google Market直接进入该程序的详细页面
Uri uri = Uri.parse("market://details?id=" + "com.demo.app");
Intent intent = new Intent(Intent.ACTION_VIEW, uri);
startActivity(intent);

//===============================================================

// 12. 进入手机设置界面:
// 进入无线网络设置界面（其它可以举一反三）  
Intent intent = new Intent(android.provider.Settings.ACTION_WIRELESS_SETTINGS);  
startActivityForResult(intent, 0);

//===============================================================

// 13. 安装apk:
Uri installUri = Uri.fromParts("package", "xxx", null);   
returnIt = new Intent(Intent.ACTION_PACKAGE_ADDED, installUri);

//===============================================================

// 14. 卸载apk:
Uri uri = Uri.fromParts("package", strPackageName, null);      
Intent it = new Intent(Intent.ACTION_DELETE, uri);      
startActivity(it); 

//===============================================================

// 15. 发送附件:
Intent it = new Intent(Intent.ACTION_SEND);      
it.putExtra(Intent.EXTRA_SUBJECT, "The email subject text");      
it.putExtra(Intent.EXTRA_STREAM, "file:///sdcard/eoe.mp3");      
sendIntent.setType("audio/mp3");      
startActivity(Intent.createChooser(it, "Choose Email Client"));

//===============================================================

// 16. 进入联系人页面:
Intent intent = new Intent();
intent.setAction(Intent.ACTION_VIEW);
intent.setData(People.CONTENT_URI);
startActivity(intent);

//===============================================================


// 17. 查看指定联系人:
Uri personUri = ContentUris.withAppendedId(People.CONTENT_URI, info.id);//info.id联系人ID
Intent intent = new Intent();
intent.setAction(Intent.ACTION_VIEW);
intent.setData(personUri);
startActivity(intent);

//===============================================================

// 18. 调用系统编辑添加联系人（高版本SDK有效）：
Intent it = newIntent(Intent.ACTION_INSERT_OR_EDIT);    
it.setType("vnd.android.cursor.item/contact");    
//it.setType(Contacts.CONTENT_ITEM_TYPE);    
it.putExtra("name","myName");    
it.putExtra(android.provider.Contacts.Intents.Insert.COMPANY, "organization");    
it.putExtra(android.provider.Contacts.Intents.Insert.EMAIL,"email");    
it.putExtra(android.provider.Contacts.Intents.Insert.PHONE,"homePhone");    
it.putExtra(android.provider.Contacts.Intents.Insert.SECONDARY_PHONE,"mobilePhone");    
it.putExtra( android.provider.Contacts.Intents.Insert.TERTIARY_PHONE,"workPhone");    
it.putExtra(android.provider.Contacts.Intents.Insert.JOB_TITLE,"title");    
startActivity(it);

//===============================================================

// 19. 调用系统编辑添加联系人（全有效）：
Intent intent = newIntent(Intent.ACTION_INSERT_OR_EDIT);    
intent.setType(People.CONTENT_ITEM_TYPE);    
intent.putExtra(Contacts.Intents.Insert.NAME, "My Name");    
intent.putExtra(Contacts.Intents.Insert.PHONE, "+1234567890");    
intent.putExtra(Contacts.Intents.Insert.PHONE_TYPE,Contacts.PhonesColumns.TYPE_MOBILE);    
intent.putExtra(Contacts.Intents.Insert.EMAIL, "com@com.com");    
intent.putExtra(Contacts.Intents.Insert.EMAIL_TYPE, Contacts.ContactMethodsColumns.TYPE_WORK);    
startActivity(intent);

//===============================================================

// 20. 打开另一程序 
Intent i = new Intent();     
ComponentName cn = new ComponentName("com.example.jay.test",     
"com.example.jay.test.MainActivity");     
i.setComponent(cn);     
i.setAction("android.intent.action.MAIN");     
startActivityForResult(i, RESULT_OK);

//===============================================================

// 21. 打开录音机
Intent mi = new Intent(Media.RECORD_SOUND_ACTION);     
startActivity(mi);

//===============================================================

// 22. 从google搜索内容 
Intent intent = new Intent();     
intent.setAction(Intent.ACTION_WEB_SEARCH);     
intent.putExtra(SearchManager.QUERY,"searchString")     
startActivity(intent);

```
