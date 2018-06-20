### Android AIDl Service

----Android远程服务Service。

**1. 远程服务与本地服务的区别**

- 远程Service与调用者不在同一个进程里（即远程Service是运行在另外一个进程）；
- 本地服务则是与调用者运行在同一个进程里。

二者详细区别：

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E5%9B%9B%E5%A4%A7%E7%BB%84%E4%BB%B6/img/service8.png)

**2. 使用场景**

----多个应用程序共享同一个后台服务（远程服务）。

即一个远程Service与多个应用程序的组件（四大组件）进行跨进程通信。

![image](https://github.com/zhaoqingyue/ZQYAndroidNotes/blob/master/%E5%9B%9B%E5%A4%A7%E7%BB%84%E4%BB%B6/img/service9.png)

**3. 具体使用**

- 为了让远程Service与多个应用程序的组件（四大组件）进行跨进程通信（IPC），需要使用AIDL

IPC：Inter-Process Communication，即跨进程通信

AIDL：Android Interface Definition Language，即Android接口定义语言；用于让某个Service与多个应用程序组件之间进行跨进程通信，从而可以实现多个应用程序共享同一个Service的功能。

- 在多进程通信中，存在两个进程角色：服务器端和客户端

以下是两个进程角色的具体使用步骤：

**服务器端（Service）**

步骤1：新建定义AIDL文件，并声明该服务需要向客户端提供的接口

步骤2：在Service子类中实现AIDL中定义的接口方法，并定义生命周期的方法（onCreat()、onBind()）

步骤3：在AndroidMainfest.xml中注册服务 & 声明为远程服务

**客户端（Client）**

步骤1：拷贝服务端的AIDL文件到目录下

步骤2：使用Stub.asInterface接口获取服务器的Binder，根据需要调用服务提供的接口方法

步骤3：通过Intent指定服务端的服务名称和所在包，绑定远程Service
