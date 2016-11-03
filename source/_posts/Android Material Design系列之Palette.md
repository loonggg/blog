---
title: Android Material Design系列之Palette
date: 2016-07-04 12:39:04
tags:
- Material Design
- Palette
categories: 
- 技术博客
- Material Design
---
>今天我们讲的这个Palette非常好用，也非常好玩。 Palette的作用是从图像中提取突出的颜色，这样我们可以根据提取到的色值把它赋给Toolbar，标题，状态栏等，可以使我们的整个界面色调统一，效果非常好看。

#### Palette介绍
Palette顾名思义调色板， Palette的作用是可以从图像中提取图片的颜色。我们可以把提取的颜色融入到App UI中，可以使UI风格更加美观融洽。
Palette可以提取的颜色如下：
* Vibrant （有活力的）
* Vibrant dark（有活力的 暗色）
* Vibrant light（有活力的 亮色）
* Muted （柔和的）
* Muted dark（柔和的 暗色）
* Muted light（柔和的 亮色）

<!--more-->
#### 通过Palette对象获取到六个样本swatch
```
Palette.Swatch s = p.getVibrantSwatch();       //获取到充满活力的这种色调
Palette.Swatch s = p.getDarkVibrantSwatch();    //获取充满活力的黑
Palette.Swatch s = p.getLightVibrantSwatch();   //获取充满活力的亮
Palette.Swatch s = p.getMutedSwatch();           //获取柔和的色调
Palette.Swatch s = p.getDarkMutedSwatch();      //获取柔和的黑
Palette.Swatch s = p.getLightMutedSwatch();    //获取柔和的亮
```
swatch对象对应的颜色方法
* getPopulation():   像素的数量
* getRgb():   RGB颜色
* getHsl():    HSL颜色
* getBodyTextColor():   用于内容文本的颜色
* getTitleTextColor():  标题文本的颜色

#### Palette实例
Palette经常用于和ViewPager，Fragment搭配使用，当我们的Pager切换时伴随着Fragment的变化，而Fragment里的内容一般是不同的，所以每个Fragment里的一般视觉效果也是不同的，所以我们可以用Palette来去提取Fragment中的主色调，把这个主色调用于整体的UI风格。

先看效果图，如下：
![](https://raw.githubusercontent.com/loonggg/MaterialDesignDemo/master/image/tmpdir__16_6_28_18_41_09.gif)
##### 第一步：添加依赖
```xml
compile 'com.android.support:palette-v7:23.4.0'
```

##### 第二步：创建Palette对象，并获取图片的颜色值
```java
// 用来提取颜色的Bitmap
Bitmap bitmap = BitmapFactory.decodeResource(getResources(), PaletteFragment.getBackgroundBitmapPosition(position));
// Palette的部分
Palette.Builder builder = Palette.from(bitmap);
builder.generate(new Palette.PaletteAsyncListener() {@Override public void onGenerated(Palette palette) {
        //获取到充满活力的这种色调
        Palette.Swatch vibrant = palette.getVibrantSwatch();
        //根据调色板Palette获取到图片中的颜色设置到toolbar和tab中背景，标题等，使整个UI界面颜色统一
        toolbar_tab.setBackgroundColor(vibrant.getRgb());
        toolbar_tab.setSelectedTabIndicatorColor(colorBurn(vibrant.getRgb()));
        toolbar.setBackgroundColor(vibrant.getRgb());

        if (android.os.Build.VERSION.SDK_INT >= 21) {
            Window window = getWindow();
            window.setStatusBarColor(colorBurn(vibrant.getRgb()));
            window.setNavigationBarColor(colorBurn(vibrant.getRgb()));
        }
    }
});
```
就是这么简单，这里略过了对TabLayout的讲解，因为这次主讲的是Palette嘛，没记错的话，以前讲解过TabLayout的使用，不会的同学可以去看源码或者是查找历史消息去看看文章。

demo的github地址：https://github.com/loonggg/MaterialDesignDemo 去star吧，我会慢慢完善的。

