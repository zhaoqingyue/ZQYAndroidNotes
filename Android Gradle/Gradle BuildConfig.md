### Gradle自定义BuildConfig

----实际开发中，服务器可能有正式环境和测试环境，gradle可以通过buildConfigField来配置。

在app/build.gradle中添加
```
buildConfigField "String", "SERVER_HEADER", "http://xxx.xxx/"
//第一个参数：数据类型，和Java的类型是对等的；
//第二个参数：常量名；
//第三个参数：配置的值

```
代码获取该常量值：

```
String SERVER_URL = BuildConfig.API_SERVER_URL;
```

具体见[Gradle签名配置](http://note.youdao.com/)