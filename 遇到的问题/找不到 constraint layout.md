### 找不到 constraint-layout:1.0.1-xxx

----打开Android Studio项目时，提示：Could not find com.android.support.constraint:constraint-layout:1.0.1。

或Failed to resolve: com.android.support.constraint:constraint-layout:1.0.1。

**解决方法：**
- a. 点击Tools -> Android -> SDK Manager 
- b. 点击SDK Tools标签 
- c. 选择show pack details，找到support repository -> constraintlayout for android。下面列表中可以看到系统中是否安装了constraint-layout:1.0.1
- d. 选择想要安装的版本，点apply，会出现下载安装有界面。

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E9%81%87%E5%88%B0%E7%9A%84%E9%97%AE%E9%A2%98/img/constrainLayout.png)