---
title: 有关使用Universal-Image-Loader的遇到的问题和使用小技巧
date: 2015-5-16 12:39:04
tags:
- Universal-Image-Loader
categories: 
- 技术博客
- 工具技巧
---
今天我们来分析一下使用Universal-Image-Loader异步加载图片时遇到的一些问题和解决办法。今天咱们的公众号不分享高大上的原理分析和源码分析，我感觉关注咱们这个公众号的开发者和程序员都希望能够解决更多开发中常见的和普通化得大众问题，所以我们会经常分享一些开发中常常遇到的一些问题的解决办法，顺便夹杂着分享一些有深度的技术文章，这样既能帮助大家解决问题，又能使大家进步。

言归正传，最近我在做一个项目时，那个项目需要展示大量的图片，对方客户上传的图片还非常大，我为了方便使用了Universal-Image-Loader图片加载的开源框架。以前没太注意，这次让我发现了很多有意思的问题，希望分享出来，帮助大家，没有遇到的也可以预防。
<!--more-->
切入正题，遇到的问题
##### 1、遇到了横屏图片（就是长大于高的图片）时，低配置的手机（或有的手机）无法显示的问题
```java
ImageLoaderConfiguration config = new ImageLoaderConfiguration.Builder(
                 getApplicationContext())
                 .memoryCacheExtraOptions(480, 800) // 保存每个缓存图片的最大长和宽
                 .threadPoolSize(3) // 线程池的大小 这个其实默认就是3
                 .threadPriority(Thread. NORM_PRIORITY - 2)//设置线程优先级
                 .denyCacheImageMultipleSizesInMemory() // 当同一个Uri获取不同大小的图片，缓存到内存时，只缓存一个。默认会缓存多个不同的大小的相同图片
                 .memoryCache( new UsingFreqLimitedMemoryCache(2 * 1024 * 1024))
                 .memoryCacheSize(2 * 1024 * 1024)// 设置缓存的最大字节
                 .tasksProcessingOrder(QueueProcessingType. LIFO)//设置图片下载和显示的工作队列排序
                 .defaultDisplayImageOptions(DisplayImageOptions. createSimple())
                 .imageDownloader(
                           new BaseImageDownloader(getApplicationContext(),
                                   5 * 1000, 30 * 1000))// connectTimeout 超时时间
                 .writeDebugLogs().build();// 开始构建
ImageLoader. getInstance().init(config);// 全局初始化此配置
```
在应用中配置ImageLoaderConfiguration参数，最好在Application中进行设置和配置，只能配置一次，如多次配置，则默认第一次的配置参数。按上面的设置和配置就解决了横屏大图在某些手机上不显示的问题。

我在接手那个项目时，真没太注意对于ImageLoader的设置与配置，这个ImageLoaderConfiguration并没有提取到Application中设置，而且在用到的地方就重新设置，而且设置了多次。请注意：其实在Application只配置一次即可。

##### 2、低配置手机中大量图片时经常发生OOM的问题
而且这个项目中很多大量图片，而且还很大，我在后台收集到的crash信息看到发生了大量的OOM现象。那是如何解决的呢？

如果在加载图片时经常出现OOM的现象，可以按照如下方式处理：
* 禁用在内存中缓存cacheInMemory(false)。
* 减少配置的线程池的大小(.threadPoolSize(...))，建议1~5
* 在显示选项中使用 .bitmapConfig(Bitmap.Config.RGB_565) 因为RGB_565模式消耗的内存比ARGB_8888模式少两倍.
* 配置中使用 .memoryCache(newWeakMemoryCache()) 或者完全禁用在内存中缓存
* 在显示选项中使用.imageScaleType(ImageScaleType.EXACTLY) 或 .imageScaleType(ImageScaleType.IN_SAMPLE_INT)
* 避免使用 RoundedBitmapDisplayer. 调用的时候它使用ARGB-8888模式创建了一个新的Bitmap对象来显示。

上面的条件，你不一定都这样设置，可能改变其中的一些选项进行设置就能解决问题。大家要灵活运用。

##### 提示和小技巧
1. 只有在你需要让Image的尺寸比当前设备的尺寸大的时候，你才需要配置maxImageWidthForMemoryCache( )和maxImageHeightForMemoryCache( )这两个参数，比如缩放图片的时候。其他情况下，不需要做这些配置，因为默认的配置会根据屏幕尺寸以最节约内存的方式处理Bitmap。

2. 在设置中配置线程池的大小。一个大的线程池会允许多条线程同时工作，但是也会显著的影响到UI线程的速度。但是可以通过设置一个较低的优先级来解决：当ImageLoader在使用的时候，可以降低它的优先级，这样UI线程会更加流畅。在使用ListView的时候，UI 线程经常会不太流畅，所以在你的程序中最好设置threadPoolSize( )和threadPriority( )这两个参数来优化你的应用。

3. memoryCache( )和memoryCacheSize( )这两个参数会互相覆盖，所以在ImageLoaderConfiguration中使用一个即可。

4. diskCacheSize( )、diskCache( )和diskCacheFileCount( )这三个参数会互相覆盖，只使用一个即可。

**特别注意：不要使用discCacheSize( )、discCache( )和discCacheFileCount( )这三个参数，因为他们已经弃用了。**

我相信这个Universal-Image-Loader很强大，使用者也很多，不知道这些细节大家发现没有？如果没发现，那你今天赚到了，如果你发现了，或者还有更多收获，更多技巧，欢迎大家在文章下面给我留言，我会整理发布给大家，让大家一起收获和成长。

