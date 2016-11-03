---
title: Android调用手机中的应用市场，去评分的功能实现
date: 2015-08-30 12:39:04
tags:
- 应用市场
- 评分
categories: 
- 技术博客
---
在我们常常使用的软件当中，我们经常可以看到在软件的设置界面，有一个功能那就是去评分的功能，只要我们一点击“去评分”就会调用手机中的应用市场软件。一开始我以为这个功能的实现是要遍历整个手机中的软件包名，去匹配市场上的大多数应用市场软件呢，这个方法一看就非常麻烦。之后，经过研究才发现其实这个功能实现非常简单。

![](http://7xsgef.com1.z0.glb.clouddn.com/640545121332121212.jpg)

**四行代码就能实现，就能够调用出您手机上所安装的所有应用市场。**

代码如下：

```java
Uri uri = Uri.parse("market://details?id="+getPackageName());


Intent intent = new Intent(Intent.ACTION_VIEW,uri);


intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);

	
startActivity(intent);
```

信不信？不信赶紧试试，相信的就赶紧收藏和分享吧，方便自己留着以后用。

