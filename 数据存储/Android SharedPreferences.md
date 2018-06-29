### Android SharedPreferences

----Sharepreferences是Android中最轻量级的数据存储。

**原理：**

----系统提供了SharedPreferences这个类, 所有用这个类存储的内容都会放在 data/data/==packagename==/shares_prefs/… 目录下以xml文件的形式存储。 

xml文件中存储的都是键值对形式的内容。一般可以存储java的简单类型(boolean, int, float, long, string, set< string > 等)。 

通常只使用SharedPreferences存储一些简单配置信息。

**使用流程**

- 获取SharedPreferences 对象
- 获取Editor
- 写入数据
- 提交数据


```
public static final String SP_NAME = "zhaoqy_sp";

public static void save(Context context, String username, String passwd) {
    SharedPreferences sp = context.getSharedPreferences(SP_NAME, Context.MODE_PRIVATE);
    SharedPreferences.Editor editor = sp.edit();
    editor.putString("username", username);
    editor.putString("passwd", passwd);
    editor.commit();
}

save(this, "zhaoqy", "123456");
```

zhaoqy_sp.xml里面的内容:

```
<?xml version='1.0' encoding='utf-8' standalone='yes' ?>
<map>
    <string name="username">zhaoqy</string>
    <string name="passwd">123456</string>
</map>
```

**读取流程**

- 获取SharedPreferences对象 
- 使用get方法读取指定值

----注意：如果SharedPreferences没有存储指定键的值，可以使用默认值代替返回值。

```
SharedPreferences sp = context.getSharedPreferences(SP_NAME, Context.MODE_PRIVATE);
String username = sp.getString("username", "");
String passwd = sp.getString("passwd", "");
```

**SharedPreferences操作模式**

模式 | 描述
---|---
Context.MODE_PRIVATE | 私有数据，只能被本身访问，写入的内容会覆盖原文件的内容
Context.MODE_APPEND | 检查文件是否存在，存在就往文件追加内容，否则就创建新文件
Context.MODE_WORLD_READABLE | 当前文件可以被其他应用读取
Context.MODE_WORLD_WRITEABLE | 当前文件可以被其他应用写入

Context.MODE_WORLD_READABLE和Context.MODE_WORLD_WRITEABLE用来控制其他应用是否有权限读写该文件。

```
SharedPreferences sp = context.getSharedPreferences(SP_NAME, Context.MODE_WORLD_READABLE);
SharedPreferences.Editor editor = sp.edit();
editor.putString("username", username);
editor.putString("passwd", passwd);
editor.commit();

// 其它app读取
SharedPreferences sp = context.getSharedPreferences(SP_NAME, Context.MODE_WORLD_READABLE);
String username = sp.getString("username", "");
String passwd = sp.getString("passwd", "");

```

**封装SharedPreferences工具**

```
/**
 * 保存数据
 */
public static void put(Context context, String key, Object obj) {
    SharedPreferences sp = context.getSharedPreferences(SP_NAME, context.MODE_PRIVATE);
    SharedPreferences.Editor editor = sp.edit();
    if (obj instanceof Boolean) {
        editor.putBoolean(key, (Boolean) obj);
    } else if (obj instanceof Float) {
        editor.putFloat(key, (Float) obj);
    } else if (obj instanceof Integer) {
        editor.putInt(key, (Integer) obj);
    } else if (obj instanceof Long) {
        editor.putLong(key, (Long) obj);
    } else {
        editor.putString(key, (String) obj);
    }
    editor.commit();
}


/**
 * 获取指定数据
 */
public static Object get(Context context, String key, Object defaultObj) {
    SharedPreferences sp = context.getSharedPreferences(SP_NAME, context.MODE_PRIVATE);
    if (defaultObj instanceof Boolean) {
        return sp.getBoolean(key, (Boolean) defaultObj);
    } else if (defaultObj instanceof Float) {
        return sp.getFloat(key, (Float) defaultObj);
    } else if (defaultObj instanceof Integer) {
        return sp.getInt(key, (Integer) defaultObj);
    } else if (defaultObj instanceof Long) {
        return sp.getLong(key, (Long) defaultObj);
    } else if (defaultObj instanceof String) {
        return sp.getString(key, (String) defaultObj);
    }
    return null;
}

/**
 * 删除指定数据
 */
public static void remove(Context context, String key) {
    SharedPreferences sp = context.getSharedPreferences(SP_NAME, context.MODE_PRIVATE);
    SharedPreferences.Editor editor = sp.edit();
    editor.remove(key);
    editor.commit();
}

/**
 * 返回所有键值对
 */
public static Map<String, ?> getAll(Context context) {
    SharedPreferences sp = context.getSharedPreferences(SP_NAME, context.MODE_PRIVATE);
    Map<String, ?> map = sp.getAll();
    return map;
}

/**
 * 删除所有数据
 */
public static void clear(Context context) {
    SharedPreferences sp = context.getSharedPreferences(SP_NAME, context.MODE_PRIVATE);
    SharedPreferences.Editor editor = sp.edit();
    editor.clear();
    editor.commit();
}

/**
 * 检查key对应的数据是否存在
 */
public static boolean contains(Context context, String key) {
    SharedPreferences sp = context.getSharedPreferences(SP_NAME, context.MODE_PRIVATE);
    return sp.contains(key);
}
```
