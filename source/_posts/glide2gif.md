---
title: Glide 展示 gif 动态图的介绍和走过的坑
date: 2016-08-21 12:39:04
tags:
- Glide
- gif
categories: 
- 技术博客
- 疑难杂症
---
>每天跟我要展示gif图demo的人很多，我有点纳闷，大家不知道很多第三方库都可以加载gif吗？比如Glide和Fresco都可以啊，大家不用它加载gif图吗？我有点纳闷，这几个方式加载的效果都还可以。当然也有点坑在里面，需要大家注意一下。

我以前写的那个关于gif图的demo其实是很早以前写的，但是那时候水平很菜，好几年前了，也不是完全自己写的，参考的别人写的，之后自己整理的。所以很不咋的。我发现每天都有人跟我要展示gif的demo，我想分享给大家展示gif图的几个好用的库。

#### Glide
如果用过Glide的同学可能大家都知道Glide是谷歌的一位员工开源的，名字叫：bumptech。这个库被广泛的运用在google的开源项目中，这个Glide说到底和Picasso很像，Glide和Picasso有90%的相似度，应该是基于Picasso开发的。但是Glide是支持动态gif图的。
<!--more-->
##### Glide gif图使用
其实和加载普通图片的方式是一样的，如下：
```java
 Glide.with(this).load("图片地址：url").asGif().into(iv);
```
这里如果使用了.asGif()方法的话，传入的图片必须是gif图，其他图会报错。当然不使用.asGif()方法同样也可以加载gif图。

##### 遇到的坑
你以为这样就可以了吗？有时候有些坑大家不得不踩，你有没有遇到过加载gif图很慢或者出不来的情况？有的话，解决办法咱也是有的，如下：
```java
Glide.with(this).load(url).asGif().diskCacheStrategy(DiskCacheStrategy.SOURCE).into(imageView);  
```
加入了缓存策略，缓存策略有四种如下：
```java
/** Caches with both {@link #SOURCE} and {@link #RESULT}. */
ALL(true, true),
/** Saves no data to cache. */
NONE(false, false),
/** Saves just the original data to cache. */
SOURCE(true, false),
/** Saves the media item after all transformations to cache. */
RESULT(false, true);
```
ALL和RESULT的缓存策略不可以，NONE是不缓存数据，SOURCE是缓存原型，原图。加上了如上的缓存策略就解决了很慢或者有时加载不出gif图的问题了。


可能大家还有一种要求就是，动态显示gif图的次数，比如我想显示一次gif就停止，也有解决办法如下：
```java
Glide.with(this).load("url").diskCacheStrategy(DiskCacheStrategy.SOURCE).into(new GlideDrawableImageViewTarget(iv, 1));
```
这里的GlideDrawableImageViewTarget(ImageView view, int maxLoopCount)这个方法，maxloopCount可以控制显示次数，你不信试试。

