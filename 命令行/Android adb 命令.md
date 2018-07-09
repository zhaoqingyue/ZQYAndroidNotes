
ADB：Android Debug Bridge，Android调试桥。

1. adb version：查看adb版本

```
adb version
```

2. adb shell：进入shell

```
adb shell
```

3. adb root：获取管理员权限

```
adb root
```

4. adb devices：获取设备列表及设备状态

```
adb devices
```

5. adb remount：重新挂载系统分区，使系统分区重新可写

```
adb remount
```

6. adb kill-server：关闭adb服务

```
adb kill-server
```
7. adb start-server：启动adb服务

```
adb start-server
```

8. adb install：安装Apk程序

   命令格式: adb install -r 应用程序.apk
   
   -r：指如果应用已存在则替换安装, 更多的参数请参考adb help.
   
```
adb install -r test.apk
[100%] /data/local/tmp/test.apk
        pkg: /data/local/tmp/test.apk
Success
```

9. adb push：安装Apk程序

   命令格式: adb push <local> <remote>
   
```
adb push test.apk  /system/app/
```

以上的两种方法都可以安装Apk, 但是也有所不同：

- adb install：将apk程序安装到data/data目录下，作为普通的用户应用程序；
- adb push：将一个文件写入手机的存储系统。

只要拥有相应的权限, 可以将任何apk放到任意目录下。

10. adb uninstall：卸载Apk程序

   命令格式: adb uninstall  package
```
adb uninstall com.xxx.xxx
```

11. adb pull：获取文件

   命令格式:adb pull <remote> <local>
   
```
adb pull /system/app/ file/
```

12. adb logcat：查看Log

   命令格式: adb logcat
   
```
logcat | grep "abc"
```
该命令查看log信息, 并且只显示包含abc的log信息。

13. adb df： 查看系统盘符

```
adb shell df
```

14. adb shell pm：输出所有已安装的应用

```
adb shell pm list packages -f
```

15. adb shell am： 启动一个activity

   命令格式: adb shell am start 包名/包名+类名
   
   例如：启动相机
  
```
adb shell am start com.android.camera/.Camera
```

16. screenrecord： 录制屏幕

    命令格式: adb shell screenrecord <存储位置>

```
adb shell screenrecord /sdcard/demo.mp4
```

17. screencap：截图

    命令格式: adb shell screencap <存储位置>

```
adb shell screencap -p /sdcard/screen.png
```
截屏，保存至 sdcard 目录

18. adb reboot： 重新启动

```
adb reboot
```

19. adb shell input：模拟按键输入

   命令格式: adb shell input keyevent 按键对应Code
   
   例如：模拟按下home键

```
adb shell input keyevent 3
```
常见的按键的Code

按键名称 | Code
---|---
menu | 82
home | 3
up | 19
down | 20
left | 21
right | 22
enter | 66
back | 4



