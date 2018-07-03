### Android Studio之gradle的buildTypes内部配置

```
android {

    compileSdkVersion 25
    buildToolsVersion "25.0.0"

    signingConfigs {
    
        debug {
            // xxx_keystore.jks文件最好放在项目app目录下
            // storeFile file("xxx_keystore.jks")
        
            // debug.keystore放在../../../keystore目录下
            storeFile file('../../../keystore/debug.keystore')
            storePassword "123456"
            keyPassword "123456"
            keyAlias "xxx"
        }
        
        release {
            // xxx_keystore.jks签名文件最好放在项目app目录下
            // storeFile file("xxx_keystore.jks")
            
            // release.keystore放在../../../keystore目录下
            storeFile file('../../../keystore/release.keystore')
            keyPassword '123456'
            storePassword '123456'
            keyAlias 'xxx'
        }
    }

    defaultConfig {
    
        applicationId "com.xxx.xxx"
        multiDexEnabled true
        minSdkVersion 16
        targetSdkVersion 25
        
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_7
            targetCompatibility JavaVersion.VERSION_1_7
        }
    }

    buildTypes {
        
        debug {
            // 测试服务器配置
            buildConfigField "String", "SERVERHEAD", "\"http://xxx/xxx/\""
            
            // 是否是测试版本-true
            buildConfigField "boolean", "IS_TESTVERSION", "true"
            
            // 是否混淆
            minifyEnabled false
            
            // 去除无用的resource
            shrinkResources false

            // Zipalign优化，内存对齐
            zipAlignEnabled true
            
            // 签名
            signingConfig signingConfigs.debug
        }
        
        release {
            // 正式服务器配置
            buildConfigField "String", "SERVERHEAD", "\"http://xxx/xxx/\""
        
            // 是否是测试版本-false
            buildConfigField "boolean", "IS_TESTVERSION", "false"

            // 是否混淆
            minifyEnabled true

            // 去除无用的resource
            shrinkResources true

            // Zipalign优化，内存对齐
            zipAlignEnabled true
            
            // 代码混淆
            // proguardFiles 'proguard-project.txt'
            // 或
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            
            // 签名
            signingConfig signingConfigs.release
        }
    }
}
```

**配置属性说明：**

- buildConfigField

----字段参数配置，Android的gradle会根据buildTypes的配置自动生成BuildConfig.java文件，直接在项目接口配置的java文件内通过类名调用获取，可以避免每次调试与正式打包时容易忘记切换服务器的问题，省去出错麻烦。

```
// 服务器header字段
public static final String SERVER_HEADER = BuildConfig.SERVERHEAD;
```

- minifyEnabled

----是否混淆，debug与release的默认值都为false。如果需要混淆，则手动配置为true；混淆文件在proguardFiles后面配置，默认为app目录下的 "proguard-rules.pro"。

- shrinkResources

----是否移除无用的resource。配置true，则移除无用的resource文件，可以减小apk包的大小，有瘦身效果。
        
- zipAlignEnabled

----是否对齐app所有资源，对齐后，会使得在运行时Android与应用程序间的交互更加有效率。
