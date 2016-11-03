---
title: Android6.0出现「java.lang.UnsatisfiedLinkError...xxx.so has text relocations」的解决办法
date: 2016-04-27 12:39:04
tags:
- UnsatisfiedLinkError
- Android6.0
- so has text relocations
categories: 
- 技术博客
---
### 遇到的问题
前几天在做有关扫一扫的功能时，使用了zxing，在Android6.0的系统上出现了如下反应：

```java
Process: com.mhealth37.BloodPressure, PID: 21082

	java.lang.UnsatisfiedLinkError: dlopen failed: /data/app/com.mhealth37.BloodPressure-1/lib/arm/libiconv.so: has text relocations

	at java.lang.Runtime.loadLibrary(Runtime.java:384)

	at java.lang.System.loadLibrary(System.java:1086)

	at com.mhealth37.butler.bloodpressure.activity.CameraScanActivity.<clinit>(CameraScanActivity.java:65)

	at java.lang.Class.newInstance(Native Method)

	at android.app.Instrumentation.newActivity(Instrumentation.java:1070)

	at android.app.ActivityThread.performLaunchActivity(ActivityThread.java:2342)

	at android.app.ActivityThread.handleLaunchActivity(ActivityThread.java:2504)

	at android.app.ActivityThread.access$900(ActivityThread.java:165)

	at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1368)

	at android.os.Handler.dispatchMessage(Handler.java:102)

	at android.os.Looper.loop(Looper.java:150)

	at android.app.ActivityThread.main(ActivityThread.java:5546)

	at java.lang.reflect.Method.invoke(Native Method)

	at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:792)

	at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:682)

```
<!--more-->
### 解决办法
随着今年国产手机ROM都逐渐升级到了Android6.0系统，我相信肯定会有很多开发者会遇到此类问题，今天我就分享一个解决办法，其实非常简单，这个libiconv.so（xx.so）文件使用了较低版本的SDK，当时我的targetSdkVersion为23，所以我就降低到了22，就不会再报错了，而且能够正常使用了。 这是libiconv.so文件的解决办法，如果你用的那个xx.so文件降低到22还报错的话，建议继续降低版本尝试。

### 例子：

```java
compileSdkVersion 23
    buildToolsVersion '23.0.3'
    defaultConfig {
        applicationId "com.loonggg.xxxx.xxxx"
        minSdkVersion 11
        targetSdkVersion 22
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"

        versionCode 55
        versionName "5.2.4"

        // dex突破65535的限制
        multiDexEnabled true
        // 默认是umeng的渠道
        manifestPlaceholders = [UMENG_CHANNEL_VALUE: "loonggg"]
    }
```


