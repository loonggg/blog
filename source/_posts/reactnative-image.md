---
title: 基础篇章：React Native之 Image 的讲解
date: 2016-10-04 10:39:04
tags:
- React Native
- 基础组件
- Image
categories: 
- 技术博客
- React Native
---
>今天一起来学习一些Image这个组件，它其实就是相当于我们android控件中的ImageView。

我们先看例子，看看加载本地图片和远程服务器图片的方式，其实差不多。
```js
import React, { Component } from 'react';
import { AppRegistry, View, Image } from 'react-native';

class DisplayAnImage extends Component {
  render() {
    return (
      <View>
        <Image
          source={require('./img/favicon.png')}
        />
        <Image
          style={{width: 50, height: 50}}
          source={{uri: 'https://facebook.github.io/react/img/logo_og.png'}}
        />
      </View>
    );
  }
}
```

第一个就是直接在source里写相对路径，第二个就是直接写图片的服务器地址即可。
<!--more-->
当然它也支持在android中显 示GIF 和 WebP 图片，方式如下：
* 在android/app/build.gradle中依赖下列开源库
   ```
   dependencies {  
   // If your app supports Android versions before Ice Cream Sandwich (API    level 14)
   compile 'com.facebook.fresco:animated-base-support:0.11.0'  
   // For animated GIF support
   compile 'com.facebook.fresco:animated-gif:0.11.0'  
   // For WebP support, including animated WebP
   compile 'com.facebook.fresco:animated-webp:0.11.0' 
   compile 'com.facebook.fresco:webpsupport:0.11.0'  
   // For WebP support, without animations
   compile 'com.facebook.fresco:webpsupport:0.11.0' 
   }
   ```
* 在proguard-rules.pro中配置防混淆
    ```
    -keep class com.facebook.imagepipeline.animated.factory.AnimatedFactoryImpl {
  public AnimatedFactoryImpl(com.facebook.imagepipeline.bitmaps.PlatformBitmapFactory, com.facebook.imagepipeline.core.ExecutorSupplier);
}
    ```

## 属性
* onLayout function 布局发生变化时调用，参数为：{nativeEvent: {layout: {x, y, width, height}}}.
* onLoad function 图片加载成功时回调该方法。
* onLoadEnd function 加载结束后，不管成功与否，都回调该方法。
* onLoadStart function 顾名思义，加载开始时调用。
* resizeMode enum('cover', 'contain', 'stretch') 决定当组件尺寸和图片尺寸不成比例的时候如何调整图片的大小。
* source {uri: string} uri是一个表示图片的资源标识的字符串，它可以是一个http地址或是一个本地文件路径（使用require(相对路径)来引用）。

## Image的style
* backfaceVisibility ['visible', 'hidden'] 隐藏或者显示
* backgroundColor color 背景色
* borderBottomLeftRadius 左下角圆角大小
* borderBottomRightRadius 
* borderColor color 边框颜色
* borderRadius 边框圆角
* borderTopLeftRadius 
* borderTopRightRadius
* borderWidth number 边框宽度
* opacity 设置透明度
* overflow ['visible', 'hidden'] 设置图片尺寸超过容器可以设置显示或者隐藏
* resizeMode 图片调整模式
* tintColor color 颜色设置
* overlayColor 当图片圆角显示时，剩余空间设置的颜色，android独有

## 例子实践
看看我模仿的QQ上的一个页面，漂不漂亮？说实话，敲代码这个东西，还得实践，看看这个效果，通过这几篇简单的学习，你能实现吗？
### 效果图
![](https://raw.githubusercontent.com/loonggg/BlogImages/master/ReactNative/image/3.png)

### 例子代码
```js
import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  Image
} from 'react-native';

class ImageDemo extends Component {
  render() {
    return (
      <View style={styles.container}>
         <View style={styles.title_view}>
         <Text style={styles.title_text}>
               空间动态
         </Text>
        </View>
        <View style={styles.three_image_view}>
         <View style={styles.vertical_view}>
              <Image source={require('./img/igs.png')} style={{alignSelf:'center',width:45,height:45}} />
              <Text style={styles.top_text}>
                好友动态
              </Text>
         </View>
          <View style={styles.vertical_view}>
              <Image source={require('./img/eqc.png')} style={{alignSelf:'center',width:45,height:45}}/>
              <Text style={styles.top_text}>
                附近
              </Text>
         </View>
          <View style={styles.vertical_view}>
              <Image source={require('./img/iei.png')} style={{alignSelf:'center',width:45,height:45}}/>
              <Text style={styles.top_text} >
                兴趣部落
              </Text>
         </View>
        </View>
        <View style={{height:30,backgroundColor:'#f9f9fb'}}/>
        <View style={styles.rectangle_view}>
          <View style={{flexDirection:'row',alignItems: 'center'}}>
              <Image source={require('./img/nsa.png')} style={{alignSelf:'center',width:30,height:30}}/>
              <Text style={styles.rectangle_text} >
                羽毛球
              </Text>
          </View>
          <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
         </View>
         <View style={styles.rectangle_view}>
          <View style={{flexDirection:'row',alignItems: 'center'}}>
              <Image source={require('./img/nsb.png')} style={{alignSelf:'center',width:30,height:30}}/>
              <Text style={styles.rectangle_text} >
                火车票
              </Text>
          </View>
          <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
         </View>
         <View style={styles.rectangle_view}>
          <View style={{flexDirection:'row',alignItems: 'center'}}>
              <Image source={require('./img/nrz.png')} style={{alignSelf:'center',width:30,height:30}}/>
              <Text style={styles.rectangle_text} >
                视频
              </Text>
          </View>
          <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
         </View>
      </View>
    );
  }
}

const styles = StyleSheet.create({
 container: {
    flex: 1,
    backgroundColor: 'white',
  },
   title_view:{
    flexDirection:'row',
    height:50,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor:'#27b5ee',
  },
   title_text:{
    color:'white',
    fontSize:20,
    textAlign:'center'
  },
  three_image_view:{
    paddingTop: 15,
    flexDirection:'row',
    justifyContent: 'space-around',
    alignItems: 'center',
    backgroundColor:'white',
  },
  vertical_view:{
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor:'white',
    paddingBottom:15,
  },
   top_text:{
    marginTop:5,
    color:'black',
    fontSize:16,
    textAlign:'center'
  },
  rectangle_view:{
    paddingTop:8,
    paddingBottom:8,
    paddingLeft:15,
    paddingRight:15,
    flexDirection:'row',
    justifyContent: 'space-between',
    alignItems: 'center',
    backgroundColor:'white',  
    borderBottomColor:'#dedfe0',
    borderBottomWidth:1,
  },
  rectangle_text:{
    color:'black',
    fontSize:16,
    textAlign:'center',
    paddingLeft:8,
  },

});

AppRegistry.registerComponent('ImageDemo', () => ImageDemo);
```

最后我想说，不懂的可以留言，或者去我的微信公众号下面留言，评论，一起交流学习。我的微信公众号：非著名程序员。







