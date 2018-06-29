### Android文件存储


**1.1 内存**

----英文中称作memory，内存是计算机中重要的部件之一，它是与CPU进行沟通的桥梁。计算机中所有程序的运行都是在内存中进行的，所以说它是用于计算机运行时的，它不是用来存储数据的。 

**1.2 内部存储、外部存储**

----内部存储称为InternalStorage，外部称为ExternalStorage，这两个概念来自于早期的Android智能机。

- Android 4.4以前，内置存储就是内部存储，外置SD卡就是外置存储。通过getDataDirectory就可以获取内置存储根路径，通过getExternalStorageDirectory就可以获取外置SD卡根路径。

- Android 4.4以后外部存储就包含两部分了，其中通过getExternalStorageDirectory获取的是机身存储的外部存储，而外置SD卡我们则需要通过getExternalDirs遍历来获取了。 

**1.3 机身存储**

----机身存储是指手机自身携带的存储空间，出厂时就已经有了。

- Android 4.4以前机身存储就是内部存储；
- Android 4.4及以后机身存储包含了内部存储和外部存储。 

**2.1 内部存储**

----内部存储中，文件默认只能被应用本身访问。

- a. 如果在创建内部存储文件时，将文件属性设置成可读，其它app能够访问该应用的数据(前提是其它app知道该应用的包名)
- b. 如果文件的属性是私有(private), 那么即使知道包名，其它app也无法访问
- c. 当一个应用卸载后，内部存储中的这些文件也被删除。
- d. SharedPreferences和SQLite数据库，都是存储在内部存储空间。
        
**注意：内部存储不是内存**

内部存储，系统在对应的包名下创建的文件夹
- cache：存放缓存数据
- databases：存放使用SQLite存储的数据
- files：存放普通数据（log数据，json型数据等）
- shared_prefs：存放使用SharedPreference存放的数据


```
/**
 * 获取内部存储API
 */
public static ArrayList<ApiInfo> getInternalApiList() {
    ArrayList<ApiInfo> list = new ArrayList<ApiInfo>();

    ApiInfo apiInfo0 = new ApiInfo();
    apiInfo0.no = 0;
    apiInfo0.type = "/data：内部存储";
    apiInfo0.api = "Environment.getDataDirectory()";
    apiInfo0.des = "获取内部存储的根路径";
    list.add(apiInfo0);
    // path： /data

    ApiInfo apiInfo1 = new ApiInfo();
    apiInfo1.no = 1;
    apiInfo1.type = "/data：内部存储";
    apiInfo1.api = "getFilesDir()";
    apiInfo1.des = "获取某个应用在内部存储中的files路径";
    list.add(apiInfo1);
    // path： /data/user/0/com.mtq.zqydatastorage/files

    ApiInfo apiInfo2 = new ApiInfo();
    apiInfo2.no = 2;
    apiInfo2.type = "/data：内部存储";
    apiInfo2.api = "getCacheDir()";
    apiInfo2.des = "获取某个应用在内部存储中的cache路径";
    list.add(apiInfo2);
    // path： /data/user/0/com.mtq.zqydatastorage/cache

    ApiInfo apiInfo3 = new ApiInfo();
    apiInfo3.no = 3;
    apiInfo3.type = "/data：内部存储";
    apiInfo3.api = "getDir()";
    apiInfo3.des = "获取某个应用在内部存储中的自定义路径";
    list.add(apiInfo3);
    // path： /data/user/0/com.mtq.zqydatastorage/app_zqy

    return list;
}
```

2.2 外部存储

----外部存储包含两部分，一是机身存储的外部存储部分，还有一个是SD卡部分。

- 小于4.4（API19）系统：

----手机自身带的存储卡就是内部存储，而扩展的SD卡就是外部存储。
        
- 大于或等于4.4系统：

----手机将机身存储（手机自身带的存储叫做机身存储）在概念上分成了" 内部存储internal" 和" 外部存储external" 两部分。扩展的SD卡也是外部存储。

```
/**
 * 获取外部存储API
 */
public static ArrayList<ApiInfo> getExternalApiList() {
    ArrayList<ApiInfo> list = new ArrayList<ApiInfo>();

    ApiInfo apiInfo4 = new ApiInfo();
    apiInfo4.no = 4;
    apiInfo4.type = "/storage：外部存储";
    apiInfo4.api = "Environment.getExternalStorageDirectory()";
    apiInfo4.des = "获取外部存储的根路径";
    list.add(apiInfo4);
    // path： /storage/emulated/0

    ApiInfo apiInfo5 = new ApiInfo();
    apiInfo5.no = 5;
    apiInfo5.type = "/storage：外部存储";
    apiInfo5.api = "getExternalStoragePublicDirectory()";
    apiInfo5.des = "获取外部存储的根路径";
    list.add(apiInfo5);
    // path： /storage/emulated/0/Music

    ApiInfo apiInfo6 = new ApiInfo();
    apiInfo6.no = 6;
    apiInfo6.type = "/storage：外部存储";
    apiInfo6.api = "getExternalFilesDir()";
    apiInfo6.des = "获取某个应用在外部存储中的files路径";
    list.add(apiInfo6);
    // path： /storage/emulated/0/Android/data/com.mtq.zqydatastorage/files

    ApiInfo apiInfo7 = new ApiInfo();
    apiInfo7.no = 7;
    apiInfo7.type = "/storage：外部存储";
    apiInfo7.api = "getExternalCacheDir()";
    apiInfo7.des = "获取某个应用在外部存储中的cache路径";
    list.add(apiInfo7);
    // path： /storage/emulated/0/Android/data/com.mtq.zqydatastorage/cache

    return list;
}
```

2.3 系统API

```
public static ArrayList<ApiInfo> getSystemApiList() {
    ArrayList<ApiInfo> list = new ArrayList<ApiInfo>();
    
    ApiInfo apiInfo8 = new ApiInfo();
    apiInfo8.no = 8;
    apiInfo8.type = "/cache：系统缓存";
    apiInfo8.api = "Environment.getDownloadCacheDirectory()";
    apiInfo8.des = "获取Android系统缓存路径";
    list.add(apiInfo8);
    // path： /cache

    ApiInfo apiInfo9 = new ApiInfo();
    apiInfo9.no = 9;
    apiInfo9.type = "/system：系统";
    apiInfo9.api = "Environment.getRootDirectory()";
    apiInfo9.des = "获取Android系统路径";
    list.add(apiInfo9);
    // path： /system
}

```

**3.1 RAM、ROM 以及扩展存储（TF卡）**

![image](https://github.com/zhaoqingyue/ZQYDataStorageDemo/blob/master/img/file.jpg)

从图中我们可以看到，一个手机里面有内存，手机内置存储，以及SD卡，分别是RAM、ROM 以及TF卡。 


**3.2 APP专属文件**

----APP专属文：属于某个具体的应用。文件路径都带有相应的包名，当APP卸载时，会随应用一起删除。当在设置里面手动清除某个应用数据时（不是清除缓存），也会一起被清掉。

Android使用这种专属文件的目的就是为了方便文件管理，避免文件随意存储，显得很乱，另一个目的就是为了当应用被卸载时不会留下很多垃圾文件。 

**3.3 App独立文件**

----App独立文件：不依赖于某特定app的文件。这类文件，当删除应用之后，还应该保留在手机上的，例如拍摄的照片，不应该随着删除应用而被删除掉。


**内部存储与外部存储都有APP专属文件，该使用哪个？**

----内部存储与外部存储都有APP专属文件，很显然应该用外部存储的。

因为内部存储本身就比较小，而且已经存储了一些系统的文件，因此内部存储我们尽量不要去使用。但是当手机没有外部存储时，还是得使用内部存储，一般程序员会做判断是否有外部存储，没有再使用内部存储，代码如下：

```
public static String getFilePath(Context context,String dir) {
    String directoryPath = "";
    if (MEDIA_MOUNTED.equals(Environment.getExternalStorageState()) ) {
        //判断外部存储是否可用 
        directoryPath = context.getExternalFilesDir(dir).getAbsolutePath();
    } else {
        //没外部存储就使用内部存储  
        directoryPath=context.getFilesDir() + File.separator+dir;
    }
    File file = new File(directoryPath);
    if(!file.exists()) {
        //判断文件目录是否存在
        file.mkdirs();
    }
    return directoryPath;
}
```

**3.4 清除数据和清除缓存到底清除了什么数据**

- /data/user/0/packname/files：用来存储普通数据 
- /data/user/0/packname/cache：用来存储缓存数据 

- 清除缓存：应用程序在运行过程中需要经过很多过程，比如读入程序，计算，输入输出等等，这些过程中肯定会产生很多的数据，它们在内存中，以供程序运行时调用。所以清除缓存清除的是APP运行过程中所产生的临时数据。 
- 清除数据：清除数据才是真正的删除了保存在文件中的数据（永久性数据，如果不人为删除的话会一直保存在文件中）。

----例如在设置里面清除了某个应用的数据，那么/data/user/0/packname/和/storage/emulated/0/Android/data/packname/下的文件里面的数据会全部删除，包括cache，files，lib，shared_prefs等等。

**3.5 不同版本getExternalStorageDirectory获取到的路径**


系统版本 | 路径
---|---
4.0 | /mnt/sdcard
4.1 | /storage/sdcard0
4.2 | /storage/sdcard0
4.4 | /storage/emulated/0
6.0 | /storage/emulated/0

**4.1 文件存储**

```
/**
 * 写入文件：输出流
 */
public static void saveFile(Context context, String fileName, String str) {
    // 创建String对象保存文件名路径
    try {
        // 创建指定路径的文件
        File file = new File(context.getFilesDir(), fileName);
        // 如果文件不存在
        if (file.exists()) {
            // 创建新的空文件
            file.delete();
        }
        file.createNewFile();
        // 获取文件的输出流对象
        FileOutputStream outStream = new FileOutputStream(file);
        // 获取字符串对象的byte数组并写入文件流
        outStream.write(str.getBytes());
        // 最后关闭文件输出流
        outStream.close();
    } catch (Exception e) {
        e.printStackTrace();
    }
}

/**
 * 删除已存储的文件
 */
public static void deletefile(Context context, String fileName) {
    try {
        // 找到文件所在的路径并删除该文件
        File file = new File(context.getFilesDir(), fileName);
        file.delete();
    } catch (Exception e) {
        e.printStackTrace();
    }
}

/**
 * 读取文件里面的内容
 */
public static String readFile(Context context, String fileName) {
    try {
        // 创建文件
        File file = new File(context.getFilesDir(), fileName);
        // 创建FileInputStream对象
        FileInputStream fis = new FileInputStream(file);
        // 创建字节数组 每次缓冲1M
        byte[] b = new byte[1024];
        int len = 0;// 一次读取1024字节大小，没有数据后返回-1.
        // 创建ByteArrayOutputStream对象
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        // 一次读取1024个字节，然后往字符输出流中写读取的字节数
        while ((len = fis.read(b)) != -1) {
            baos.write(b, 0, len);
        }
        // 将读取的字节总数生成字节数组
        byte[] data = baos.toByteArray();
        // 关闭字节输出流
        baos.close();
        // 关闭文件输入流
        fis.close();
        // 返回字符串对象
        return new String(data);
    } catch (Exception e) {
        e.printStackTrace();
        return null;
    }
}
```




