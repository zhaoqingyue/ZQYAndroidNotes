### OkHttp学习笔记

----Square开源的轻量级Android网络请求框架

> Square还贡献了Retrofit & Picasso & LeakCanary

GitHub地址：https://github.com/square/okhttp 

**1. 添加依赖**

在app/build.gradle添加依赖
```
compile 'com.squareup.okhttp3:okhttp:3.6.0'
```

**2. OkHttp使用**

- 创建OkHttpClient，添加配置
- 创建请求
- 执行请求

**2.1 创建OkHttpClient，添加配置**
```
OkHttpClient okHttpClient = new OkHttpClient.Builder().build();  
```

OkHttpClient配置

```
File cacheDir = new File(getCacheDir(), "okhttp_cache");  
// File cacheDir = new File(getExternalCacheDir(), "okhttp");  
Cache cache = new Cache(cacheDir, 10 * 1024 * 1024);  
  
OkHttpClient okHttpClient = new OkHttpClient.Builder()  
            .connectTimeout(5*1000, TimeUnit.MILLISECONDS)        // 链接超时  
            .readTimeout(10*1000, TimeUnit.MILLISECONDS)          // 读取超时  
            .writeTimeout(10*1000, TimeUnit.MILLISECONDS)         // 写入超时  
            .addInterceptor(new HttpHeadInterceptor())            // 应用拦截器：统一添加消息头  
            .addNetworkInterceptor(new NetworkspaceInterceptor()) // 网络拦截器  
            .addInterceptor(loggingInterceptor)                   // 应用拦截器：打印日志  
            .cache(cache)                                         // 设置缓存  
            .build();  
```



**2.2 创建请求**

----通过Request.Builder创建请求

**2.2.1 GET请求**
```
String url = "https://www.baidu.com/";
Request request = new Request.Builder()
        .url(url)
        .build();  
okHttpClient.newCall(request);  
```

**2.2.2 POST请求**

----构建RequestBody，并设置Content-Type消息头

- 普通Post请求
```
RequestBody requestBody = RequestBody.create(MediaType.parse("application/json"), jsonObject.toString());  
Request request = new Request.Builder()
        .url(url)  
        .post(requestBody)  
        .build();  
okHttpClient.newCall(request);  
```

- 表单POST请求
```
RequestBody requestBody = new FormBody.Builder()  
                    .add("username", "zhaoqy")  
                    .add("password", "123456")  
                    .build();  
Request request = new Request.Builder()
        .url(url)
        .post(requestBody)
        .build();  
okHttpClient.newCall(request);  
```

- 表单上传文件

```
RequestBody uploadImg = RequestBody.create(MediaType.parse("image/jpeg"), file);  
MultipartBody uploadBody = new MultipartBody.Builder()  
        .setType(MultipartBody.FORM)  
        .addFormDataPart("username", "zhaoqy")  
        .addFormDataPart("password", "123456")  
        .addFormDataPart("img", fileName, uploadImg)  
        .build();  
Request request = new Request.Builder()
        .url(url)
        .post(uploadBody)
        .build();  
okHttpClient.newCall(request); 
```

**2.3 执行请求**

**2.3.1 同步执行**
```
Response response = call.execute();  
```

> Android要求网络请求在线程中执行，所以无法使用execute

**2.3.2 异步执行**

```
call.enqueue(new Callback() {  

    @Override  
    public void onFailure(Call call, IOException e) {  
        // 失败回调  
    }  
  
    @Override  
    public void onResponse(Call call, Response response) throws IOException {  
        // 成功回调，当前线程为子线程，如果需要更新UI，需要post到主线程中  
        boolean successful = response.isSuccessful();  
        
        // 响应消息头  
        Headers headers = response.headers();  
        
        // 响应消息体  
        ResponseBody body = response.body();  
        String content = response.body().string());  
        
        // 缓存控制  
        CacheControl cacheControl = response.cacheControl();  
    }  
});  
```
