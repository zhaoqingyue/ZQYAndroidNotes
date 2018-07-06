### Gradle基本配置

在Android Studio新建一个项目后，目录结构如下图所示：

![image](http://note.youdao.com/favicon.ico)

**1. app/build.gradle**

----初始化的Gradle配置。

```
apply plugin: 'com.android.application'

android {

    compileSdkVersion 25
    buildToolsVersion "27.0.3"
    
    defaultConfig {
        applicationId "com.mtq.zqygradle"
        minSdkVersion 19
        targetSdkVersion 25
        versionCode 1
        versionName "1.0"
    }
    
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
    compile 'com.android.support:appcompat-v7:25.3.1'
    testCompile 'junit:junit:4.12'
}
```

- apply plugin: 'com.android.application'：表示该module是一个app module，应用了com.android.application插件

**PS：**
如果是一个android library，此处写为：apply plugin: 'com.android.library'

- compileSdkVersion：编译的目标Android版本
- buildToolsVersion：编译工具版本
- defaultConfig：默认配置
- applicationId：包名
- minSdkVersion：应用支持的最低SDK版本
- targetSdkVersion：应用支持的最高SDK版本
- versionCode：版本号
- versionName：版本名称
- buildTypes：构建类型

**PS：**
常用的构建类型有release和debug两种，可以在里面启用混淆，启用zipAlign以及配置签名信息等

- dependencies：定义该module需要依赖的jar，aar，jcenter库信息

**2. gradle-wrapper.properties**

----声明gradle的目录与下载路径以及当前项目使用的gradle版本。

该项目的gradle版本为：2.14.1

```
#Mon Dec 28 10:00:20 PST 2015
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-2.14.1-all.zip
```

**3. 根目录build.gradle**

----定义在整个工程下的所有模块的公共属性。

```
buildscript {

    repositories {
        jcenter()
    }
    
    dependencies {
        // 依赖android提供的2.2.2的gradle build
        classpath 'com.android.tools.build:gradle:2.2.2'
        
        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

// 为所有的工程的repositories配置为jcenters
allprojects {

    repositories {
        jcenter()
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}
```


**4. setting.gradle**

----包含整个项目的模块，比如有app和library：

```
include ':app'
include ':library'
```

