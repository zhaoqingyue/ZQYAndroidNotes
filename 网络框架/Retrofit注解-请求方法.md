### Retrofit注解-请求方法

**1. 网络请求方法**

- @GET
- @POST
- @PUT
- @DELETE
- @PATH
- @HEAD
- @OPTIONS
- @HTTP

初始化Retrofit
```
String BASE_URL = "http://102.10.10.132/api/";

Retrofit retrofit = new Retrofit.Builder() 
        .baseUrl(BASE_URL)
        .addConverterFactory(GsonConverterFactory.create())
        .build();
```

**1.1 @GET**

- 无参数
```
http://102.10.10.132/api/News

@GET("News")
Call<News> getItem();
```

- @Path：有参数
```
http://102.10.10.132/api/News/1
http://102.10.10.132/api/News/{资讯id}

@GET("News/{newsId}")
Call<News> getItem(@Path("newsId") String newsId);
```

- @Query：参数在URL问号之后
```
// ？问号后：1个参数
http://102.10.10.132/api/News?newsId=1
http://102.10.10.132/api/News?newsId={资讯id}

@GET("News")
Call<News> getItem(@Query("newsId") String newsId);

// ？问号后：多个参数

http://102.10.10.132/api/News?newsId=1&type=类型1
http://102.10.10.132/api/News?newsId={资讯id}&type={类型}

@GET("News")
Call<News> getItem(@Query("newsId") String newsId， @Query("type") String type);
```

- @QueryMap：多个参数在URL问号之后，且个数不确定
```
http://102.10.10.132/api/News?newsId=1&type=类型1...
http://102.10.10.132/api/News?newsId={资讯id}&type={类型}...

@GET("News")
Call<News> getItem(@QueryMap Map<String, String> map);

// 接口调用
Map<String, String> map = new HashMap<String, String>();
map.put("newsId", "newsId");
map.put("type", "type");
getItem(map);

或者：
@GET("News")
Call<News> getItem(@Query("newsId") String newsId，@QueryMap Map<String, String> map);
```

**1.2 @POST**

- @Path + @Field：补全URL，只有一条post数据reason
```
http://102.10.10.132/api/Comments/1
http://102.10.10.132/api/Comments/{newsId}

@FormUrlEncoded
@POST("Comments/{newsId}")
Call<Comment> reportComment(
        @Path("newsId") String commentId,
        @Field("reason") String reason);
```

- @Path + @Query + @Field：补全URL，问号后加入access_token，只有一条post数据reason
```
http://102.10.10.132/api/Comments/1?access_token=1234123
http://102.10.10.132/api/Comments/{newsId}?access_token={access_token}

@FormUrlEncoded
@POST("Comments/{newsId}")
Call<Comment> reportComment(
        @Path("newsId") String commentId,
        @Query("access_token") String access_token,
        @Field("reason") String reason);
```

- @Path + @Query + @Body：补全URL，问号后加入access_token，post一个body（对象）
```
http://102.10.10.132/api/Comments/1?access_token=1234123
http://102.10.10.132/api/Comments/{newsId}?access_token={access_token}

@POST("Comments/{newsId}")
Call<Comment> reportComment(
        @Path("newsId") String commentId,
        @Query("access_token") String access_token,
        @Body CommentBean bean);
```

**1.3 PUT**

```
http://102.10.10.132/api/Accounts/1
http://102.10.10.132/api/Accounts/{accountId}

@PUT("Accounts/{accountId}")
Call<ExtrasBean> updateExtras(
        @Path("accountId") String accountId,
        @Query("access_token") String access_token,
        @Body ExtrasBean bean);
```

**1.4 DELETE**

- @Path：补全URL
```
http://102.10.10.132/api/Comments/1
http://102.10.10.132/api/Comments/{commentId}

@DELETE("Comments/{commentId}")
Call<ResponseBody> deleteComment(
        @Path("commentId") String commentId);
```

- @Path + @Query：补全URL，问号后加入access_token
```
http://102.10.10.132/api/Comments/1?access_token=1234123
http://102.10.10.132/api/Comments/{commentId}?access_token={access_token}

@DELETE("Comments/{commentId}")
Call<ResponseBody> deleteComment(
        @Path("commentId") String commentId，
        @Query("access_token") String access_token);
```

- @Body：带有body
```
http://102.10.10.132/api/Comments

@HTTP(method = "DELETE", path = "Comments", hasBody = true)
Call<ResponseBody> deleteCommont(@Body CommentBody body);
```

**@HTTP**

----可以替代其它请求方法的任意一种
```
/**
 * method 表示请的方法，不区分大小写
 * path表示路径
 * hasBody表示是否有请求体
 */
@HTTP(method = "get", path = "users/{user}", hasBody = false)
Call<ResponseBody> getFirstBlog(@Path("user") String user);
```


**2. 总结**

- @Path：所有在网址中的参数（URL的问号前面）
```
http://102.10.10.132/api/Accounts/{accountId}
```

- @Query：URL问号后面的参数，如：
```
http://102.10.10.132/api/Comments?access_token={access_token}
```

- @QueryMap：相当于多个@Query
- @Field：用于POST请求，提交单个数据
- @Body：相当于多个@Field，以对象的形式提交

**Tips**
- 使用@Field时，记得添加@FormUrlEncoded
- 使用@body时，不要加@FormUrlEncoded
- 若需要重新定义接口地址，可以使用@Url，将地址以参数的形式传入即可。如
```
@GET
Call<List<Activity>> getActivityList(
    @Url String url,
    @QueryMap Map<String, String> map);
            
String url = "http://115.159.198.162:3001/api";
Call<List<Activity>> call = service.getActivityList(url, map);
```


