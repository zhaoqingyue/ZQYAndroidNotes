### RxPermissions

**开源项目**

https://github.com/tbruyelle/RxPermissions

**导入依赖**

```
compile 'com.tbruyelle.rxpermissions:rxpermissions:0.9.3@aar'
```

---- 基于RxJava开发的用于帮助在Android 6.0中处理运行时权限检测的框架。

在Android 6.0中，系统新增了部分权限的运行时动态获取，而不再是在以前的版本中安装的时授予权限。

### 用法

1. 创建RxPermission实例

```
RxPermissions rxPermission = new RxPermissions(this);
```

2. 获取权限
- Observable.compose(rxPermissions.ensure())

```
rxPermissions.request(Manifest.permission.CAMERA)
    .subscribe(new Action1<Boolean>() {

        @Override
        public void call(Boolean aBoolean) {
            if (aBoolean) {
                Toast.makeText(getApplicationContext(),
                    "all permissions get success",  Toast.LENGTH_SHORT).show();
            }else {
                Toast.makeText(getApplicationContext(),
                    "must has one ore more permissions get failed", Toast.LENGTH_SHORT).show();
            }
        }
});
```

- Observable.compose(rxPermissions.ensureEach())

```
rxPermissions.requestEach(Manifest.permission.CAMERA, Manifest.permission.RECORD_AUDIO)
    .subscribe(new Action1<Permission>() {

        @Override
        public void call(Permission permission) {
            if (permission.granted){
                Toast.makeText(getApplicationContext(), permission.name + "this permission get success", Toast.LENGTH_SHORT).show();
            } else {
                Toast.makeText(getApplicationContext(), permission.name + "this permission get failed", Toast.LENGTH_SHORT).show();
            }
        }
});
```

- new RxPermissions(this).request(permissions)

```
 RxView.clicks(btn_ensure)
    .compose(rxPermissions.ensure(Manifest.permission.CAMERA))
    .subscribe(new Action1<Boolean>() {
    
        @Override
        public void call(Boolean aBoolean) {
            if (aBoolean) {
                Toast.makeText(getApplicationContext(), "all permissions get success", Toast.LENGTH_SHORT).show();
            } else {
                Toast.makeText(getApplicationContext(), "must has one ore more permissions get failed", Toast.LENGTH_SHORT).show();
            }
        }
});
```

- new RxPermissions(this).requestEach(permissions)

```
RxView.clicks(btn_ensureEach)
    .compose(rxPermissions.ensureEach(Manifest.permission.CAMERA, Manifest.permission.RECORD_AUDIO))
    .subscribe(new Action1<Permission>() {

        @Override
        public void call(Permission permission) {
            if (permission.granted){
                Toast.makeText(getApplicationContext(), permission.name + "this permission get success", Toast.LENGTH_SHORT).show();
            } else {
                Toast.makeText(getApplicationContext(), permission.name + "this permission get failed", Toast.LENGTH_SHORT).show();
            }
        }
});
```

### 总结

- 在初始程序的时候请求一个权限： new RxPermissions(this).request(permissions) or new RxPermissions(this).requestEach(permissions)
- 在初始程序的时候请求多个权限： new RxPermissions(this).requestEach(permissions)
- 在指定的位置，时机请求一个权限：Observable.compose(rxPermissions.ensure()) or Observable.compose(rxPermissions.ensureEach())
- 在指定的位置，时机请求多个权限：Observable.compose(rxPermissions.ensureEach())
