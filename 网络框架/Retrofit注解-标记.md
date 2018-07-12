### Retrofit注解-标记

- @FormUrlEncoded
- @Multipart
- @Streaming

**1.1 @FormUrlEncoded**

----用表单提交数据：用于POST请求，请求体是Form表单，每个键值对用@Filed来注解键名

```
@FormUrlEncoded
@POST("tasks")
Call<Task> createTask(@Field("id") String id);

@FormUrlEncoded
@POST("tasks")
Call<Task> createTask(@Field("id") String id, @Field("name") String name);

@FormUrlEncoded
@POST("tasks")
Call<Task> createTask(@FieldMap Map<String, String> map);

注意：
1. 使用@FormUrlEncoded时，不能使用GET（Form encoded requests are intended to send data to the server!）；
2. 使用@Field(key)来定义请求参数，key来定义请求参数中的关键值；
3. @Field的编码选择encoded，默认为false；
4. @FieldMap可定义一个Map类型的数据类型方便用于多参数的请求。
```

**1.2 @Multipart**

----请求体是一个支持文件上传的Form表单（一般用户上传文件）

**1.3 @Streaming**

----数据以流的形式返回（用于下载大文件）
```
@Streaming
@GET
Call<ResponseBody> downloadFile(@Url String fileUrl);

ResponseBody body = response.body();
long fileSize = body.contentLength();
InputStream inputStream = body.byteStream();
```
