---
title: Android6.0运行时权限的处理及解决办法
date: 2016-04-28 12:39:04
tags:
- Android6.0
- 权限处理
categories: 
- 技术博客
---

## 问题背景
随着今年Android6.0系统的国产ROM终于来了，适配6.0已经势在必行了。最近也有很多开发者在微信私聊和咨询我遇到的问题，其中有一些都是关于Android6.0权限的，我感觉有必要写一篇文章来解惑了。Runtime Permissions在6.0系统的几个变化中，还是比较大的。

如果你在Android6.0系统的手机上如果遇到了这样的错误，必定跟其运行时权限有关。

**open failed: EACCES (Permission denied)**

## 权限变化

在Adroid系统6.0以前，权限的处理是在App安装时授权，授权完了才能完成相关的安装。而在6.0的系统上，是先安装App，在安装完之后，在使用相关权限的操作时，才会弹出权限的提示框，用户同意授权之后才能正常使用。谷歌这样做，可以让用户更加清醒的认识相关权限的使用，在一定程度上更加人性化和保护了用户的隐私。

谷歌官方将权限分为了两类，一个是正常权限（Normal Permissions），这类权限不涉及用户隐私，是不需要用户进行授权的，比如访问网络，手机震动等。还有一类是危险权限（Dangerous Permissions），一般是涉及到用户隐私的，需要用户进行授权，比如操作SD卡的写入，相机，录音等。
<!--more-->
### Normal Permissions:
> ACCESS_LOCATION_EXTRA_COMMANDS
> ACCESS_NETWORK_STATE
> ACCESS_NOTIFICATION_POLICY
> ACCESS_WIFI_STATE
> BLUETOOTH
> BLUETOOTH_ADMIN
> BROADCAST_STICKY
> CHANGE_NETWORK_STATE
> CHANGE_WIFI_MULTICAST_STATE
> CHANGE_WIFI_STATE
> DISABLE_KEYGUARD
> EXPAND_STATUS_BAR
> GET_PACKAGE_SIZE
> INSTALL_SHORTCUT
> INTERNET
> KILL_BACKGROUND_PROCESSES
> MODIFY_AUDIO_SETTINGS
> NFC
> READ_SYNC_SETTINGS
> READ_SYNC_STATS
> RECEIVE_BOOT_COMPLETED
> REORDER_TASKS
> REQUEST_INSTALL_PACKAGES
> SET_ALARM
> SET_TIME_ZONE
> SET_WALLPAPER
> SET_WALLPAPER_HINTS
> TRANSMIT_IR
> UNINSTALL_SHORTCUT
> USE_FINGERPRINT
> VIBRATE
> WAKE_LOCK
> WRITE_SYNC_SETTINGS

### Dangerous Permissions:
![](http://7xsgef.com1.z0.glb.clouddn.com/Screen20Shot202015-09-1220at207.37.png)

## 解决方法
看完官方文档，还好解决方案还不是太难，也不是非常麻烦，当然和以前相比还是有一点繁琐的。废话不多说了，解决方案如下。

### 检查系统版本
```java
private boolean canMakeSmores(){

    return(Build.VERSION.SDK_INT>Build.VERSION_CODES.LOLLIPOP_MR1);

}
```
检查系统版本的原因，显而易见，如果是系统6.0及以上的，需要采用新的权限授权方法。

### 申请授权
举个例子，如果你需要拍照操作，那么你就应该在拍照操作的地方，先加上权限申请授权。方式如下：
```java
String[] perms = {"android.permission.CAMERA"};

int permsRequestCode = 200; 

requestPermissions(perms, permsRequestCode);
```

### 授权回调处理
```java
@Override

public void onRequestPermissionsResult(int permsRequestCode, String[] permissions, int[] grantResults){

    switch(permsRequestCode){

        case 200:

            boolean cameraAccepted = grantResults[0]==PackageManager.PERMISSION_GRANTED;
			if(cameraAccepted){
				//授权成功之后，调用系统相机进行拍照操作等
			}else{
				//用户授权拒绝之后，友情提示一下就可以了
			}

            break;

    }

}
```
### 进一步处理和完善
通过上面可以看出，只检查系统版本了，如果授权过的权限，还需要再次去授权么？我们的判断其实可以更完善一下，比如：
```java
private boolean hasPermission(String permission){

    if(canMakeSmores()){

        return(checkSelfPermission(permission)==PackageManager.PERMISSION_GRANTED);

    }

    return true;

}
```
上面这段代码，是先判断系统版本，再判断这个权限是否已经授权过，这样就不会重复了。

还有就是比如如果用户第一次已经拒绝过这个权限，下次再操作时怎么办呢？还有一个更人性化的方法就是：
```java
shouldShowRequestPermissionRationale(@NonNull String permission)
```
这个方法的作用：
1. 第一次请求权限时，用户拒绝了，下一次：shouldShowRequestPermissionRationale()  返回 true，应该显示一些为什么需要这个权限的说明

2. 第二次请求权限时，用户拒绝了，并选择了“不在提醒”的选项时：shouldShowRequestPermissionRationale()  返回 false

3. 设备的策略禁止当前应用获取这个权限的授权：shouldShowRequestPermissionRationale()  返回 false 

## 完结
到这里就基本介绍完结了，希望能够对大家有所帮助，能够进一步理解Android6.0运行时权限的使用。继续加油吧，学习永无止境。

