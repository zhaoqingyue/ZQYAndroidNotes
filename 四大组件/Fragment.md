### Fragment

> Android 3.0（API 11）后引入，Activity片段。Fragment不能单独使用，需要嵌套在Activity中使用

**1. Fragment概述**




**2. Fragment生命周期**

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E5%9B%9B%E5%A4%A7%E7%BB%84%E4%BB%B6/img/fragment_lifecycle.png)

**2.1 生命周期的11个方法**

> Activity生命周期有7个方法

- onAttach()：当Fragment被添加到Activity时回调，只会回调一次
- onCreate()：创建Fragment时回调，只会回调一次
- onCreateView()：每次创建 & 绘制该Fragment的View组件时回调
- onActivityCreate()：当Fragment所在的Activity启动完成时回调
- onStart()：启动Fragment时回调
- onResume()：恢复Fragment时回调；onStart()后一定回调onResume()

> onStart()-可见；onResume()-可交互

- onPause()：暂停Fragment时回调
- onStop()：停止Fragment时回调
- onDestroyView()：销毁Fragment所包含的View组件时调用
- onDestroy()：销毁Fragment时回调
- onDetach()：将该Fragment从Activity删除 / 替换后回调；onDestroy()后一定回调onDetach()，只回到一次

**Fragment生命周期3种状态**
- Resumed：Fragment可见
- Paused：所在Activity可见，但是得不到焦点
- Stoped: 调用addToBackStack()，Fragment被添加到Bcak栈，该Activity转向后台，或者该Fragment被替换 / 删除


