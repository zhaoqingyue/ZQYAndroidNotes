### Universal-Image-Loader

----Android图片加载框架。

**1. 功能特性**

- 多线程异步加载和显示图片

----图片来源：网络、SD卡、assets文件夹、drawable文件夹(不能加载.9图片)、视频缩略图、ContentProvider。

网络
```
// from Web  
"http://site.com/image.png"
```

D卡
```
// from SD card 
"file:///mnt/sdcard/image.png"

// from SD card (video thumbnail)  
"file:///mnt/sdcard/video.mp4" 
```

assets文件夹
```
// from assets  
 "assets://image.png" 
```

drawable文件夹
```
// from drawables (non-9patch images)  
"drawable://" + R.drawable.img 
```

ContentProvider
```
// from content provider  
"content://media/external/images/media/13" 

// from content provider (video thumbnail)  
"content://media/external/video/media/13" 
```

- 支持Listener监听加载过程

- 缓存图片至内存

- 高度可定制化

- 支持图片的内存缓存，SD卡缓存

**2. 配置详解**

**2.1 Eclipse配置**

----下载jar放在libs文件夹中

**2.2 Android Studio配置**

----在gradle中添加

```
compile 'com.nostra13.universalimageloader:universal-image-loader:1.9.3'  
```

**2.3 AndroidManifest.xml中添加权限**

```
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />  
<uses-permission android:name="android.permission.INTERNET" />  
```

**2.4 在Application中配置ImageLoaderConfiguration参数**

----只能配置一次，如多次配置，则默认第一次的配置参数。

- 使用默认设置

```
ImageLoaderConfiguration configuration = ImageLoaderConfiguration.createDefault(this);   
```

- 自己配置参数

```
// 注：根据自己的需要进行配置，不是所有的都要进行配置
File cacheDir = StorageUtils.getCacheDirectory(context);  //缓存文件夹路径  
ImageLoaderConfiguration config = new ImageLoaderConfiguration.Builder(context)  
        .memoryCacheExtraOptions(480, 800) // default = device screen dimensions 内存缓存文件的最大长宽  
        .diskCacheExtraOptions(480, 800, null)  // 本地缓存的详细信息(缓存的最大长宽)，最好不要设置这个   
        .taskExecutor(...)  
        .taskExecutorForCachedImages(...)  
        .threadPoolSize(3) // default  线程池内加载的数量  
        .threadPriority(Thread.NORM_PRIORITY - 2) // default 设置当前线程的优先级  
        .tasksProcessingOrder(QueueProcessingType.FIFO) // default  
        .denyCacheImageMultipleSizesInMemory()  
        .memoryCache(new LruMemoryCache(2 * 1024 * 1024)) //可以通过自己的内存缓存实现  
        .memoryCacheSize(2 * 1024 * 1024)  // 内存缓存的最大值  
        .memoryCacheSizePercentage(13) // default  
        .diskCache(new UnlimitedDiscCache(cacheDir)) // default 可以自定义缓存路径    
        .diskCacheSize(50 * 1024 * 1024) // 50 Mb sd卡(本地)缓存的最大值  
        .diskCacheFileCount(100)  // 可以缓存的文件数量   
        // default为使用HASHCODE对UIL进行加密命名， 还可以用MD5(new Md5FileNameGenerator())加密  
        .diskCacheFileNameGenerator(new HashCodeFileNameGenerator())   
        .imageDownloader(new BaseImageDownloader(context)) // default  
        .imageDecoder(new BaseImageDecoder()) // default  
        .defaultDisplayImageOptions(DisplayImageOptions.createSimple()) // default  
        .writeDebugLogs() // 打印debug log  
        .build(); //开始构建  
```

配置好ImageLoaderConfiguration，一定要在application中进行初始化。

```
ImageLoader.getInstance().init(config);  
```

**2.5 图片显示操作**

- 要得到ImageLoader的实例(使用的单例模式)

```
// 注：在每个显示任务（布局中都需实例化才能进行相关操作
ImageLoader imageLoader = ImageLoader.getInstance();   
```

- 相关显示参数配置

```
DisplayImageOptions options = new DisplayImageOptions.Builder()  
        .showImageOnLoading(R.drawable.ic_stub) // 设置图片下载期间显示的图片  
        .showImageForEmptyUri(R.drawable.ic_empty) // 设置图片Uri为空或是错误的时候显示的图片  
        .showImageOnFail(R.drawable.ic_error) // 设置图片加载或解码过程中发生错误显示的图片  
        .resetViewBeforeLoading(false)  // default 设置图片在加载前是否重置、复位  
        .delayBeforeLoading(1000)  // 下载前的延迟时间  
        .cacheInMemory(false) // default  设置下载的图片是否缓存在内存中 
        .cacheOnDisk(false) // default  设置下载的图片是否缓存在SD卡中  
        .preProcessor(...)  
        .postProcessor(...)  
        .extraForDownloader(...)  
        .considerExifParams(false) // default  
        .imageScaleType(ImageScaleType.IN_SAMPLE_POWER_OF_2) // default 设置图片以如何的编码方式显示  
        .bitmapConfig(Bitmap.Config.ARGB_8888) // default 设置图片的解码类型  
        .decodingOptions(...)  // 图片的解码设置  
        .displayer(new SimpleBitmapDisplayer()) // default  还可以设置圆角图片new RoundedBitmapDisplayer(20)  
        .handler(new Handler()) // default  
        .build();  
```

注：如果DisplayImageOption没有传递给ImageLoader.displayImage(…)方法，那么从配置默认显示选项
(ImageLoaderConfiguration.defaultDisplayImageOptions(…))将被使用。

```
1）.imageScaleType(ImageScaleType imageScaleType)  //设置图片的缩放方式  
    缩放类型mageScaleType:  
    EXACTLY :图像将完全按比例缩小的目标大小  
    EXACTLY_STRETCHED:图片会缩放到目标大小完全  
    IN_SAMPLE_INT:图像将被二次采样的整数倍  
    IN_SAMPLE_POWER_OF_2:图片将降低2倍，直到下一减少步骤，使图像更小的目标大小    
    NONE:图片不会调整  
    
2）.displayer(BitmapDisplayer displayer)   //设置图片的显示方式  
    显示方式displayer：  
    RoundedBitmapDisplayer（int roundPixels）设置圆角图片  
    FakeBitmapDisplayer（）这个类什么都没做  
    FadeInBitmapDisplayer（int durationMillis）设置图片渐显的时间  
    SimpleBitmapDisplayer()正常显示一张图片  
```

- 显示图片

```

1. ImageLoader.getInstance().displayImage(uri, imageView);  
2. ImageLoader.getInstance().displayImage(uri, imageView, options);  
3. ImageLoader.getInstance().displayImage(uri, imageView, listener);  
4. ImageLoader.getInstance().displayImage(uri, imageView, options, listener);  
5. ImageLoader.getInstance().displayImage(uri, imageView, options, listener, progressListener);  
    
// imageUrl   图片的URL地址  
// imageView  显示图片的ImageView控件    
// options    DisplayImageOptions配置信息   
// listener   图片下载情况的监听  
// progressListener  图片下载进度的监听  
```

方法1：最简单的方式，只需要定义要显示的图片的URL和要显示图片的ImageView。这种情况下，图片的显示选项会使用默认的配置

方法2：加载自定义配置的一个图片

方法3：加载带监听的一个图片

方法4：加载自定义配置且带监听的一个图片

方法5：加载自定义配置且带监听和进度条的一个图片

```
ImageLoader.getInstance().displayImage(uri, imageView, options,  
        new ImageLoadingListener() {  
      
        @Override  
        public void onLoadingStarted(String arg0, View arg1) {  
            //开始加载  
        }  
      
        @Override  
        public void onLoadingFailed(String arg0, View arg1,  FailReason arg2) {  
            //加载失败  
        }  
      
        @Override  
        public void onLoadingComplete(String arg0, View arg1,  Bitmap arg2) {  
            //加载成功  
        }  
      
        @Override  
        public void onLoadingCancelled(String arg0, View arg1) {  
            //加载取消  
        }  
            
    }, new ImageLoadingProgressListener() {  
      
        @Override  
        public void onProgressUpdate(String imageUri, View view,  int current, int total) {  
            //加载进度  
        }  
});  
```
