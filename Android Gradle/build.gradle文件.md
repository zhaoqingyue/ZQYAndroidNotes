### Android build.gradle

----文件目录：app/build.gradle

**前言**

----Android Studio使用Gradle编译运行Android工程，工程的每个模块以及整个工程都有一个build.gradle文件。

- 工程模块：build.gradle(Module：app)

- 整个工程：build.gradle(Project：工程名)

通常只需要关注模块的build.gradle文件，该文件存放编译依赖设置，包括defaultConfig设置。

defaultConfig设置：

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/Android%20Gradle/img/gradle1.png)

- compiledSdkVersion：将要编译的目标Android版本，默认SDK已安装的最新版本

- buildToolsVersion：编译工具版本

- applicationId：包名

- minSdkVersion：应用支持的最低SDK版本

- targetSdkVersion：应用支持的最高SDK版本