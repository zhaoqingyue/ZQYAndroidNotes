### Retrofit注解-请求参数


建议：
- baseUrl: 总是以 / 结尾
- url: 不要以 / 开头

**1. 网络请求参数**
- @Header
- @Headers
- @URL
- @Body
- @Path
- @Query
- @QueryMap
- @Part
- @PartMap
- @Field
- @FieldMap

**1.1 @Header & @Headers**

----添加请求头

@Header：添加不固定的请求头，作用于参数
```
@GET("user")
Call<User> getUser(@Header("Authorization") String authorization)
```

@Headers：添加固定的请求头，作用于方法
```
@Headers("Authorization: authorization")
@GET("user")
Call<User> getUser()
```

@Headers定义多个固定请求头：

```
@Headers({
    "Authorization: authorization",
    "apikey:b86c2269fe6588bbe3b41924bb2f2da2"})
@GET("user")
Call<User> getUser()
```

**1.2 @URL**

----直接传入一个请求的URL，用于URL设置（适用于非统一baseUrl的场景）

```
@GET
Call<ResponseBody> getProfilePicture(@Url String url);
```

**1.3 @Body**

----以Post方式传递自定义数据类型给服务器
```
// 定义接口
public interface TaskService {  

    // POST方法
    @POST("/tasks")
    Call<Task> createTask(@Body Task task);
}

// 自定义数据类型
public class Task {  
    private long id;
    private String text;

    public Task(long id, String text) {
        this.id = id;
        this.text = text;
    }
}

// 调用接口
Task task = new Task(1, "title");  
Call<Task> call = taskService.createTask(task);  
call.enqueue(new Callback<Task>() {
    
});


调用createTask()后，tas对象将被Json格式数据替换，如下：
{
    "id": 1,
    "text": "my task title"
}
```

**1.4 @Path**

----URL地址的缺省值(替换请求url中{xxx}中xxx)

```
// 访问接口的BaseUrl
baseUrl("https://api.github.com/")

// 访问接口
@GET("users/{user}/repos")
Call<ResponseBody> getBlog(@Path("user") String user);

// 调用接口
getBlog("zhaoqy");
// 访问的API：https://api.github.com/users/zhaoqy/repos
// 发起请求时， {user} 被替换为参数user
```

**1.5 @Query & @QueryMap**

----查询参数：键值对，Retrofit把@Query的字段拼接到接口中

```
// 访问接口的BaseUrl
baseUrl("http://apis.baidu.com/txapi/")

// 访问接口
@GET("word/word")
Call<News> getNews(@Query("num") String num, @Query("page")String page);

或

@GET("News")
Call<News> getNews(@QueryMap Map<String, String> map);

// 调用接口
getNews("10", "1");

或

Map<String, String> map = new HashMap<String, String>();
map.put("num", "10");
map.put("page", "1");
getNews.(map);

// 访问的API：http://apis.baidu.com/txapi/world/world?num=10&page=1
```

@QueryMap可以约定是否需要encode
```
@GET("News")
Call<News> getNews((@QueryMap(encoded=true) Map<String, String> map);
```

**1.6 @Part & @PartMap**

----发送POST请求时，提交请求的表单字段

```
// @Part("key") RequestBody：参数
// @Part MultipartBody.Part：文件
@POST("/form")
@Multipart
Call<ResponseBody> upload(
    @Part("name") RequestBody name, 
    @Part("age") RequestBody age, 
    @Part MultipartBody.Part file);

@POST("/form")
@Multipart
Call<ResponseBody> upload(
    @PartMap Map<String, RequestBody> args, 
    @Part MultipartBody.Part file);

```

**1.7 @Field & @FieldMap**

----向POST表单传入键值对

```
@FormUrlEncoded
@POST("tasks")
Call<Task> createTask(@Field("id") String id);
// createTask("10");

@FormUrlEncoded
@POST("tasks")
Call<Task> createTask(@Field("id") String id, @Field("name") String name);
// createTask("10", "zhaoqy");

@FormUrlEncoded
@POST("tasks")
Call<Task> createTask(@FieldMap Map<String, String> map);
// Map<String, String> map = new HashMap<String, String>();
// map.put("id", "10");
// map.put("name", "zhaoqy");
// createTask(map);
```
