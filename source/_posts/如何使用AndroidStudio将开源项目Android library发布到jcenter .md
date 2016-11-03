---
title: 如何使用AndroidStudio将开源项目library发布到jcenter
date: 2016-05-11 02:37:23
categories: 技术博客
tags: 
- jcenter
- library
- 开源
---
>我相信技术爱好者都喜欢开源，也都喜欢分享，随着技术的慢慢提高，很多开发者想将自己的开源类库分享出来，对于Android开发者来说，以前使用Eclipse开发时，直接引用类库或者打成jar包分享出来就行，现在使用AndroidStudio开发，引用类库直接在**build.gradle**文件里面添加一行引用代码即可。我们带着问题进入分析。

## 为什么只需要添加一行引用代码即可

AndroidStudio是从Maven Repository服务器下载类库的。基本上有jcenter和Maven Central这两个服务器用于下载Android的类库。 一开始AndroidStudio是使用MavenCentral作为默认的远程仓库下载类库的。但是MavenCentral有一些问题没有解决。首先是对于开发者不友好，开发者想上传自己的类库到MavenCentral非常困难，所以后来AndroidStudio团队决定把默认远程仓库换成jcenter。

所以我们在gradle里经常能够看到如下的代码配置：
```
allprojects {
    repositories {
        jcenter()
    }
}
```
这就是jcenter仓库的配置。
 <!--more-->
## 引用类库代码的含义
在gradle引用类库的时候，我们经常看到这样形式的代码：
```
compile 'com.loonggg.godcoder.view:circleprogressbar:1.0.0'
```
我们从中可以看出这行代码用：分成了三部分：xxxx:xxx:xx，其实意思就是：GROUP_ID:ARTIFACT_ID:VERSION。其实GROUP_ID对应的是**com.loonggg.godcoder.view:circleprogressbar**，相当于我们建立项目时的包名，ARTIFACT_ID对应的是**circleprogressbar**，相当于这个类库的名称，后边的VIESION对应的非常显而易见，就是1.0.0，相当于版本控制的代号。

## 如何上传到jcenter
### 申请bintray账号

#### 在bintray.com上注册一个账号。
网址：[https://bintray.com/](https://bintray.com/ "https://bintray.com/")
#### 新建一个maven类型的仓库
1. 新建一个仓库
![](http://7xsgef.com1.z0.glb.clouddn.com/11.png)
2. 选择类型，填写仓库名字，填写介绍信息
![](http://7xsgef.com1.z0.glb.clouddn.com/2.png)
仓库名字有用，一定得记住，类型要选择maven，别选择错了。

3. 在新建的maven类型仓库下Add New Package
![](http://7xsgef.com1.z0.glb.clouddn.com/3.png)
4. 填写必要信息，然后点击Create Package
![](http://7xsgef.com1.z0.glb.clouddn.com/4.png)
package名和许可证，版本号必填，其他不重要，暂时可不填写

### 在AS中配置信息，上传类库
#### 配置账号用户名和key
我们需要设置bintray的username和API Key来进行bintray的加密认证。这些信息将写在local.properties文件里。如下：
```
bintray.user=YOUR_BINTRAY_USERNAME
bintray.apikey=YOUR_BINTRAY_API_KEY
```
bintray.user里对应的就是你注册账号的用户名，apikey可以在个人信息编辑里找到。如下：
![](http://7xsgef.com1.z0.glb.clouddn.com/6.png)
#### 配置Library的module的build.gradle文件
在build.gradle文件中添加的代码如下：
```
ext {
    bintrayRepo = 'loonggg'
    bintrayName = 'CircleProgressBar'

    publishedGroupId = 'com.loonggg.godcoder.view'
    libraryName = 'CircleProgressBar'
    artifact = 'circleprogressbarlibrary'

    libraryDescription = 'CircleProgressBar is the dynamic display of a circle progress control.come on'

    siteUrl = 'https://github.com/loonggg/CircleProgressBar'
    gitUrl = 'https://github.com/loonggg/CircleProgressBar.git'

    libraryVersion = '1.0.1'

    developerId = 'loonggg'
    developerName = 'gengguanglong'
    developerEmail = 'gengguanglong@gmail.com'

    licenseName = 'The Apache Software License, Version 2.0'
    licenseUrl = 'http://www.apache.org/licenses/LICENSE-2.0.txt'
    allLicenses = ["Apache-2.0"]
}
```
* bintrayRepo对应的就是建立的maven仓库时填写的名字
* bintrayName对应的就是新建的package的名字
* publishedGroupId对应的就是我们之前提到的GROUP_ID
* artifact对应的就是之前我们提到的ARTIFACT_ID
* libraryVersion顾名思义就是VERSION

在build.gradle文件的最末行添加下面两行代码
```
apply from: 'https://raw.githubusercontent.com/nuuneoi/JCenter/master/installv1.gradle'
apply from: 'https://raw.githubusercontent.com/nuuneoi/JCenter/master/bintrayv1.gradle'
```
#### 配置project的build.gradle文件
如下：
```
 dependencies {
        classpath 'com.android.tools.build:gradle:2.0.0'
        classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.6'
        classpath "org.jfrog.buildinfo:build-info-extractor-gradle:4.0.0"
        classpath 'com.github.dcendents:android-maven-gradle-plugin:1.3'
    }
```
### 命令行操作上传
在AndroidStudio提供的Terminal窗口执行如下命令:
```
gradlew bintrayUpload
```
如果上传成功的话，几分钟后会提示BUILD SUCCESSFUL。

猜猜，这个类库引用的代码是什么样子的？如下：
```
compile 'com.loonggg.godcoder.view:circleprogressbarlibrary:1.0.1'
```

### 将上传成功的类库add到JCenter
如果以为仅仅上传成功就能用了，就大错特错了，需要将类库添加到Jcenter中之后，审核过后才能用，方式如下：
![](http://7xsgef.com1.z0.glb.clouddn.com/8.png)
输入你的包名，找到你上传的类库，接下来会跳到一个Request to include package CircleProgressBar in jcenter的页面，写一下相关的介绍或者不写也可以，直接点击Send按钮就可以了。接下来等着审核就行了，大约1小时之内就能审核通过。

收到成功的消息后，就可以直接用了，等着吧，到这里就讲完了。

