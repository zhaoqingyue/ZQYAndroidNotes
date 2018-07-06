### Gradle依赖管理

**1. 本地依赖**

- jar包

----默认情况下，新建的Android项目，app目录下会有一个lib文件夹。
```
dependencies {
    // 即添加所有在libs文件夹中的jar
    compile fileTree(dir: 'libs', include: ['*.jar'])
    // 不需要这样一个个去写了
    // compile files('libs/eventbus_3.0.0.jar')
    // compile files('libs/picasso-2.5.2.jar')
}
```
- so库

----用c或者c++写的library叫做so库，Android插件默认情况下支持native包，需要把.so文件放在对应的文件夹中

```
app
   ├── AndroidManifest.xml
   └── jniLibs
       ├── armeabi
       │   └── libhmi_opengl_android.so
       ├── armeabi-v7a
       │   └── libhmi_opengl_android.so
       ├── mips
       │   └── libhmi_opengl_android.so
       └── x86
           └── libhmi_opengl_android.so

```

- library工程

----依赖library库

```
dependencies {
    // compile project(':library名字')
    compile project(':NiceSpinner')
    
    //多个library，libraries是文件夹名字
    compile project(':libraries:library名字')
}

```

- aar文件

----library库输出文件是.aar文件，包含了Android资源文件。

生成目录：library工程build/output/aar/下

**使用方法：**

a. app目录下创建一个aars文件夹

b. 把aar文件拷贝到该文件夹里面

c. 添加该文件夹作为依赖库：app/bulid.gradle

```
android {
    //...
}

repositories {
    flatDir {
        dirs 'aars' 
    }
}

dependencies {
    compile(name:'libraryname', ext:'aar')
}
```

**生成.aar和.jar文件方法：**

----在Android Studio的Terminal执行 ./gradlew assembleRelease命令

- .aar路径：build/outputs/aar
- .jar路径：build/intermediates/bundles

**.jar和.aar区别：**

- .jar：只包含class文件与清单文件，不包含资源文件
- .aar：包含所有资源，class以及res资源文件

**注意：**

----如果library依赖了第三方库aar，须app再次依赖。比如：app依赖lib1，lib1依赖了lib2 的aar，需要：

```
dependencies {
    compile(name: 'library1-release', ext: 'aar')
    compile(name: 'library2-release', ext: 'aar')
}
```

**2. 远程仓库**

```
dependencies {
    compile 'com.android.support:appcompat-v7:25.3.1'
    compile 'com.android.support:recyclerview-v7:25.3.1'
}
```
