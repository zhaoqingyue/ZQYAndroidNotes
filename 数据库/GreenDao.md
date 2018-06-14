# ZQYGreenDaoDemo

**该demo是GreenDao3.2使用详解（增，删，改，查，升级）**

### 第一步： 配置build.grade

1. projet 目录下的build.gradle

    
```

repositories {

    ..........

    // add repository
    mavenCentral()
}

dependencies { 

    ..........
    
    // add plugin
    classpath 'org.greenrobot:greendao-gradle-plugin:3.2.2'
}
```
 
2. moudle 下的build.gradle添加如下内容： 


```
// apply plugin
apply plugin: 'org.greenrobot.greendao'  

..........
  
 dependencies {  
 
    ..........
    
    // add library
    compile 'org.greenrobot:greendao:3.2.0'  
}
```

添加完成之后点击sync一下工程。


### 第二步：配置greendao版本信息

1. 在moudle 下的build.gradle根目录下添加如下配置

    
```
greendao {  
    //数据库的schema版本，也可以理解为数据库版本号  
    schemaVersion 1  
        
    //设置DaoMaster、DaoSession、Dao包名，也就是要放置这些类的包的全路径
    daoPackage 'com.zqy.greendaodemo.dao'
        
    //设置DaoMaster、DaoSession、Dao目录  
    targetGenDir 'src/main/java'  
}
```

2. 添加实体类Student如下所示：

```
@Entity
public class Student {

    /**
     * 注意：id必须定义为Long型
     */
    @Id(autoincrement = true)
    private Long id; 
    private String name;
    private int age;
    private String num;
}
```

3. 点击 android studio 的 build-->make project，运行后在com.zqy.greendaodemo.dao目录下生成

```
DaoMaster
DaoSession
StudentDao
```
3个java文件，同时Student类里面的内容发生了变化如下：

```
@Entity
public class Student {

    @Id(autoincrement = true)
    private Long id;
    private String name;
    private int age;
    private String num;

    @Generated(hash = 727290593)
    public Student(Long id, String name, int age, String num) {
        this.id = id;
        this.name = name;
        this.age = age;
        this.num = num;
    }
    @Generated(hash = 1556870573)
    public Student() {
    }
    public Long getId() {
        return this.id;
    }
    public void setId(Long id) {
        this.id = id;
    }
    public String getName() {
        return this.name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return this.age;
    }
    public void setAge(int age) {
        this.age = age;
    }
    public String getNum() {
        return this.num;
    }
    public void setNum(String num) {
        this.num = num;
    }
}
```
GreenDao自动生成了一些get和set方法，以及构造方法。

### 第三步：为了方便使用添加一个 db包

1. DbManager类如下：


```

public class DbManager {

    private static final String DB_NAME = "student.db";
    private static DbManager mDbManager;
    private static DaoMaster.DevOpenHelper mDevOpenHelper;
    private static DaoMaster mDaoMaster;
    private static DaoSession mDaoSession;

    private Context mContext;

    private DbManager(Context context) {
        this.mContext = context;
        // 初始化数据库信息
        mDevOpenHelper = new DaoMaster.DevOpenHelper(context, DB_NAME);
        getDaoMaster(context);
        getDaoSession(context);
    }

    public static DbManager getInstance(Context context) {
        if (null == mDbManager) {
            synchronized (DbManager.class) {
                if (null == mDbManager) {
                    mDbManager = new DbManager(context);
                }
            }
        }
        return mDbManager;
    }

    ........
    
}

```


2. SutdentDaoOpe类如下: 


```

public class StudentDaoOpe {

    /**
     * 添加数据至数据库
     */
    public static void insertData(Context context, Student stu) {
        DbManager.getDaoSession(context).getStudentDao().insert(stu);
    }

   ........
}

```

具体数据库操作示例看代码

### 第四步：数据库的升级（新建一个helper包）
1. 新建MigrationHelper类：


```

/**
 * 数据库更新辅助类
 *
 * 原理：创建临时表-->删除原表-->创建新表-->复制临时表数据到新表并删除临时表；这样数据库表的更新就完成了
 */

public class MigrationHelper {

    private static final String CONVERSION_CLASS_NOT_FOUND_EXCEPTION = "MIGRATION HELPER - CLASS DOESN'T MATCH WITH THE CURRENT PARAMETERS";
    private static MigrationHelper instance;

    public static MigrationHelper getInstance() {
        if (instance == null) {
            instance = new MigrationHelper();
        }
        return instance;
    }

    ......
}

```

2. 新建MyOpenHelper类： 

```

public class MyOpenHelper extends DaoMaster.OpenHelper {

    public MyOpenHelper(Context context, String name, SQLiteDatabase.CursorFactory factory) {
        super(context, name, factory);
    }

    /**
     * 数据库升级
     */
    @Override
    public void onUpgrade(Database db, int oldVersion, int newVersion) {

        .......
    }
}

```

然后将DbManag里面的

```
/**  
 * 获取DaoMaster  
 */  
public static DaoMaster getDaoMaster(Context context) {  
    if (null == mDaoMaster) {  
        synchronized (DbManager.class) {  
            if (null == mDaoMaster) {  
                mDaoMaster = new DaoMaster(getWritableDatabase(context));  
            }  
        }  
    }  
    return mDaoMaster;  
} 
```

替换成

```
/**
 * 获取DaoMaster
 *
 * 判断是否存在数据库，如果没有则创建数据库
 */
 public static DaoMaster getDaoMaster(Context context) {
     if (null == mDaoMaster) {
        synchronized (DbManager.class) {
            if (null == mDaoMaster) {
                MyOpenHelper helper = new MyOpenHelper(context,DB_NAME,null);
                mDaoMaster = new DaoMaster(helper.getWritableDatabase());
            }
        }
    }
    return mDaoMaster;
}
```

3. 在Student类里面添加一个  sex属性，然后将build.gradle里面schemaVersion改为 2
4. 编译、运行程序，升级后的数据库：成功添加了新增的字段，还保留了以前的数据。

具体demo看代码。



