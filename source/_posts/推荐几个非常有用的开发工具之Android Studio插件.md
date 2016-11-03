---
title: 推荐几个非常有用的开发工具之Android Studio插件
date: 2015-07-09 22:37:23
categories: 技术博客
tags: AndroidStudio插件
---
>我们都知道Eclipse开发Android将在今年年底google不再继续提供相应的开发支持，转而开始强烈发展Android Studio，现在我就分享几款能帮助团队提升工作效率的几个Android Studio插件和工具。

## SelectorChapek

SelectorChapek是一款帮助我们快速完成Selector的AndroidStudio插件。安装方法如下：

1. 选择Preferences→Plugins→Browse repositories搜索SelectorChapek安装
1. 下载并在Preferences→Plugins→Install plugin from disk选择安装

![](https://raw.githubusercontent.com/loonggg/BlogImages/master/as插件/select_option.png)
<!--more-->
## ParcelableGenerator

ParcelableGenerator可以将任意对象转换为Parcelable类型，方便对象传输。

在Android中，对象的序列化一般有两种方式，一种是Serializable，一种是Parcelable。

Serializable 在Java中就存在，效率较低。

Parcelable 是Android中提供的，也是官方推荐的方式，效率比Serializable高很多。

虽然Parcelable效率高，但是使用起来比Serializable麻烦很多，很多人不使用Parcelable就是因为写法太麻烦，尤其是属性特别多的时候，我们要将每个属性Parcel.write()然后在Parcel.read()回来，相当繁琐，不如Serializable简单粗暴，直接有效。ParcelableGenerator可以解决Parcelable使用麻烦的问题，让使用Parcelable的简单性可以和使用Serializable相媲美。

![](https://raw.githubusercontent.com/loonggg/BlogImages/master/as插件/parcelable_generator.png)


## GsonFormat

现在大多数服务端api都以json数据格式返回，而客户端需要根据api接口生成相应的实体类，这个插件把这个过程自动化了，赶紧使用起来吧。

![](https://raw.githubusercontent.com/loonggg/BlogImages/master/as插件/gson_format.gif)

## ButterKnife Zelezny

俗话说：“不会偷懒的程序员不是好的程序员！”。作为一名Android开发者，是不是厌烦了大量的findViewById以及setOnClickListener代码，而ButterKnife是一个专注于Android系统的View注入框架，让你从此从这些烦人臃肿的代码中解脱出来。

![](https://raw.githubusercontent.com/loonggg/BlogImages/master/as插件/zelezny_animated.gif)

## LeakCanary

LeakCanary 是一个检测内存泄露的开源类库。你可以在 debug 包种轻松检测内存泄露。

如果大家不会用的话，附带一个中文版的使用说明：[http://www.liaohuqiu.net/cn/posts/leak-canary-read-me/](http://www.liaohuqiu.net/cn/posts/leak-canary-read-me/ "LeakCanary中文使用说明")

![](https://raw.githubusercontent.com/loonggg/BlogImages/master/as插件/leakcaneray.png)

**工欲善其事必先利其器，所以说会用工具，善用工具，可以大大提高我们的开发效率哦。**



