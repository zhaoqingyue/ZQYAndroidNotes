### Retrofit学习笔记

----Square出品

### 1. 介绍

----Retrofit是一个RESTful的网络请求框架（基于OkHttp），该网络请求的工作本质是OkHttp完成，而Retrofit仅负责网络请求接口的封装。

![image](https://github.com/zhaoqingyue/ZQYRetrofitDemo/blob/master/img/1.png)

- App应用程序通过Retrofit请求网络，实际上是使用Retrofit接口层封装请求参数、Header、url等信息，然后由OkHttp完成后续的请求操作；
- 在服务端返回数据之后，OkHttp将原始的结果交给Retrofit，Retrofit根据用户的需求对结果进行解析。

### 2. 功能
- 基于OkHttp & 遵循RESTful API设计风格；
- 通过注解配置网络请求参数；
- 支持同步 & 异步网络请求；
- 支持多种数据的解析 & 序列化格式（Gson、Json、XML）
- 提供对RxJava支持。

### 3. 优点
- 功能强大：支持同步 & 异步、支持支持多种数据的解析 & 序列化格式、支持RxJava；
- 简洁易用：通过注解配置网络请求参数、采用大量设计模式简化使用；
- 可拓展性好：功能模块高度封装、解耦彻底，如自定义Converters等。

### 4. 应用场景

----任何网络请求的需求。

### 5. 使用介绍
----使用Retrofit共有7步。

**（1）添加Retrofit库的依赖；**

- A. 在build.gradle添加Retrofit库的依赖：
```
dependencies {
    compile 'com.squareup.retrofit2:retrofit:2.0.2'
}
```

- B. 在AndroidManifest.xml添加网络权限：
```
<uses-permission android:name="android.permission.INTERNET"/>
```

- C. 添加响应转换器依赖：
```
compile 'com.squareup.retrofit2:converter-gson:2.1.0'
```

**（2）创建接收服务器返回的数据类；**

Reception.java
```
public class Reception {
    ...
    // 根据返回数据的格式和数据解析方式（Json、XML等）定义
}
```

**（3）创建用于描述网络请求的接口；**

----Retrofit Http请求抽象成Java接口：采用注解描述网络请求参数和配置网络请求参数；

A. 用动态代理将该接口的注解"翻译"成一个Http请求，最后再执行Http请求；

B. 接口中的每个方法的参数都需要使用注解标注，否则会报错。

```
public interface GetRequest_Interface {

    @GET("openapi.do?keyfrom=Yanzhikai&key=2032414398&type=data&doctype=json&version=1.1&q=car")
    Call<Translation>  getCall();
    // @GET注解的作用:采用Get方法发送网络请求

    // getCall() = 接收网络请求数据的方法
    // 其中返回类型为Call<*>，*是接收数据的类（即上面定义的Translation类）
    // 如果想直接获得Responsebody中的内容，可以定义网络请求返回值为Call<ResponseBody>
}
```

**（4）创建Retrofit实例对象；**

```
Retrofit retrofit = new Retrofit.Builder()
    .baseUrl("http://fanyi.youdao.com/") // 设置网络请求的Url地址
    .addConverterFactory(GsonConverterFactory.create()) // 设置数据解析器
    .addCallAdapterFactory(RxJavaCallAdapterFactory.create()) // 支持RxJava平台
    .build();
```

**（5）创建网络请求接口实例，并配置网络请求参数；**

```
// 创建 网络请求接口 的实例
GetRequest_Interface request = retrofit.create(GetRequest_Interface.class);

//对 发送请求 进行封装
Call<Reception> call = request.getCall();
```

**（6）发送网络请求（异步 / 同步）；**

```
//发送网络请求(异步)
call.enqueue(new Callback<Translation>() {

    //请求成功时回调
    @Override
    public void onResponse(Call<Translation> call, Response<Translation> response) {
        //请求处理,输出结果
        response.body().show();
    }

    //请求失败时候的回调
    @Override
    public void onFailure(Call<Translation> call, Throwable throwable) {
        System.out.println("连接失败");
    }
});

// 发送网络请求（同步）
Response<Reception> response = call.execute();
```

**（7）处理服务器返回的数据。**

----通过response类的 body（）对返回的数据进行处理。

```
// 对返回数据进行处理
response.body().show();
```

### 6. 注解

- [网络请求方法](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E7%BD%91%E7%BB%9C%E6%A1%86%E6%9E%B6/Retrofit%E6%B3%A8%E8%A7%A3-%E8%AF%B7%E6%B1%82%E6%96%B9%E6%B3%95.md)
- [网络请求参数](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E7%BD%91%E7%BB%9C%E6%A1%86%E6%9E%B6/Retrofit%E6%B3%A8%E8%A7%A3-%E8%AF%B7%E6%B1%82%E5%8F%82%E6%95%B0.md)
- [标记](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E7%BD%91%E7%BB%9C%E6%A1%86%E6%9E%B6/Retrofit%E6%B3%A8%E8%A7%A3-%E6%A0%87%E8%AE%B0.md)


