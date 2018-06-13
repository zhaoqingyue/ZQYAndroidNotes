### Android Picasso

----译为“毕加索”，是Android中一个图片加载开源库。

**1. 功能介绍**

（1）图片的异步加载（最基础功能）

```
ImageView targetImageView = (ImageView) findViewById(R.id.ImageView);
String Url = "http://218.192.170.132/1.jpg";

//Picasso使用了流式接口的调用方式
//Picasso类是核心实现类。
//实现图片加载功能至少需要三个参数：
    Picasso
//with(Context context)
//Context对于很多Android API的调用都是必须的，这里就不多说了
        .with(context)

//load(String imageUrl)：被加载图像的Url地址。
//大多情况下，一个字符串代表一个网络图片的URL。
        .load(Url)

//into(ImageView targetImageView)：图片最终要展示的地方。
        .into(targetImageView);
```

（2） 图片转换

```
Picasso.with(context)
    .load(url)
    //裁剪图片尺寸
    .resize(50, 50)
    //设置图片圆角
    .centerCrop()
    .into(imageView)
```

（3）加载过重 & 错误处理

```
Picasso.with(context)
    .load(url)
     //加载过程中的图片显示
    .placeholder(R.drawable.user_placeholder)
     //加载失败中的图片显示
     //如果重试3次，还是无法成功加载图片，则用错误占位符图片显示
    .error(R.drawable.user_placeholder_error)
    .into(imageView);
```

（4）在Adapter中的回收不在视野的ImageView和取消已经回收的ImageView下载进程

```
@Override 
public void getView(int position, View convertView, ViewGroup parent) {
  SquaredImageView view = (SquaredImageView) convertView;
  if (view == null) {
    view = new SquaredImageView(context);
  }
  String url = getItem(position);
  Picasso.with(context).load(url).into(view);
}

```

（5）从不同资源源加载

----支持多种数据源 网络、本地、资源、Assets 等

```
//加载资源文件
Picasso.with(context).load(R.drawable.landing_screen).into(imageView1);
//加载本地文件
Picasso.with(context).load(new File("/images/oprah_bees.png")).into(imageView2);

```

（6）自动添加磁盘和内存二级缓存功能

（7）“无”本地缓存

----“无”本地缓存，不是说没有本地缓存，而是 Picasso 自己没有实现，交给了 Square 的另外一个网络库 okhttp 去实现，这样的好处是可以通过请求 Response Header 中的 Cache-Control 及 Expired 控制图片的过期时间。

**2. 使用Picasso**

- 在gradle添加依赖

```
compile 'com.squareup.picasso:picasso:2.5.2'
```
- 添加网络权限

```
<uses-permission android:name="android.permission.INTERNET"/>
```

- 加载图片

```
ImageView targetImageView = (ImageView) findViewById(R.id.ImageView);
String Url = "http://218.192.170.132/1.jpg";

Picasso
    .with(this)
    .load(Url)
    .into(targetImageView);

```

**3. 特点**

（1）优点
- 使用简单、方便（一行代码的事情）
- 由于同样是出品自Square的，Square 公司的其他开源库如 Retrofit 或者 OkHttp和Picasso搭配使用兼容性会更好些，占用体积也会少些

（2）缺点
- 功能较为简单-图片加载；
- 性能（加载速度等等）较其他图片加载库（Glide、Fresco）较差
- 自身无实现“本地缓存”