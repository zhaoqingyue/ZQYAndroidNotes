### Gradle全局设置

**全局设置的方式：**

- 根目录build.gradle

----如果有很多项目，可以设置全局来统一管理版本号或依赖库。

根目录下build.gradle添加：

```
def androidSupportVersion = '25.3.1'

ext {
    //编译的SDK版本
    compileSdkVersion = 25
    
    //构建工具的版本
    buildToolsVersion = "27.0.3"
    
    //兼容的最低SDK版本
    minSdkVersion = 14
    
    //支持的最高SDK版本
    targetSdkVersion = 25
    
    appcompatV7 = "com.android.support:appcompat-v7:$androidSupportVersion"
    design = "com.android.support:design:$androidSupportVersion"
    supportV4 = "com.android.support:support-v4:$androidSupportVersion"
}
```

app/build.gradle使用：

```
android {

    compileSdkVersion rootProject.ext.compileSdkVersion
    buildToolsVersion rootProject.ext.buildToolsVersion
    
    defaultConfig {
        applicationId "com.mtq.zqygradle"
        minSdkVersion rootProject.ext.minSdkVersion
        targetSdkVersion rootProject.ext.targetSdkVersion
        versionCode 1
        versionName "1.0"
    }
}

dependencies {
    // ...
    compile rootProject.ext.appcompatV7
}
```

- 新建config.gradle

使用步骤：

a. 在根目录下新建个config.gradle
```
ext {
    android = [
            compileSdkVersion: 25,
            buildToolsVersion: "27.0.3",
            minSdkVersion    : 19,
            targetSdkVersion : 25,
    ]
    
    dependencies = [
            appcompatV7': 'com.android.support:appcompat-v7:25.3.1',
            design      : 'com.android.support:design:25.3.1'
    ]
}
```

b. 在根目录下build.gradle最顶部加以下代码
```
// 所有子项目或所有modules都可以从这个配置文件里读取内容
apply from: "config.gradle"
```

c. sync同步

app/build.gradle中使用：

```
android {

    compileSdkVersion rootProject.ext.android.compileSdkVersion
    buildToolsVersion rootProject.ext.buildToolsVersion
    
    defaultConfig {
        applicationId "com.mtq.zqygradle"
        minSdkVersion rootProject.ext.android.minSdkVersion
        targetSdkVersion rootProject.ext.android.targetSdkVersion
        versionCode 1
        versionName "1.0"
    }
    
    ...
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    testCompile 'junit:junit:4.12'
    compile rootProject.ext.dependencies.appcompatV7
    compile rootProject.ext.dependencies.design
}
```

- gradle.properties

根项目下的gradle.properties：

```
COMPILE_SDK_VERSION=25
BUILDTOOLS_VERSION=27.0.3
SUPPORT_LIB_VERSION=25.3.1
MIN_SDK_VERSION=19
TARGET_SDK_VERSION=25
```

app/build.gradle中使用：

```
android {

    compileSdkVersion Integer.parseInt(COMPILE_SDK_VERSION)
    buildToolsVersion BUILDTOOLS_VERSION
    
    defaultConfig {
        applicationId "com.mtq.zqygradle"
        minSdkVersion Integer.parseInt(MIN_SDK_VERSION)
        targetSdkVersion Integer.parseInt(TARGET_SDK_VERSION)
        versionCode 1
        versionName "1.0"
    }
    ...
}

dependencies {
    compile "com.android.support:support-v4:${SUPPORT_LIB_VERSION}"
    compile "com.android.support:appcompat-v7:${SUPPORT_LIB_VERSION}"
}
```
