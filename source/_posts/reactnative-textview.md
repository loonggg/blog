---
title: 基础篇章：React Native 之 View 和 Text 的讲解
date: 2016-10-03 10:39:04
tags:
- React Native
- 基础组件
- View
- Text
categories: 
- 技术博客
- React Native
---
>从今天开始我们进入基础组件也就是一些简单控件的学习，之前写的文章貌似太正式了，我正在考虑怎么样才能写的有意思，想想挺难的，算了，写一步算一步吧。今天我们就从最简答的View和Text控件讲起。

## View
View其实就是UI最基础的组件，跟我们android中的View不同，它更像我们android中的LinearLayout，RN中的View是一个支持Flexbox布局、样式、一些触摸处理和一些无障碍功能的容器，并且它可以放到其它的视图里，也可以有任意多个任意类型的子视图。不论在什么平台上，View都会直接对应一个平台的原生视图，无论它是UIView、div还是android.view.View。

下面我们看一下官方给的例子，官网例子创建了一个View，包含了两个有颜色的方块和一个自定义的组件，并且设置了一个内边距：
```js
class ViewColoredBoxesWithText extends Component { 
  render() { 
    return ( 
      <View style={{flexDirection: 'row', height: 100, padding: 20}}> 
        <View style={{backgroundColor: 'blue', flex: 0.3}} /> 
        <View style={{backgroundColor: 'red', flex: 0.5}} /> 
        <Text>Hello World!</Text> 
      </View> 
      ); 
  } 
}
```
>View样式的使用初衷并鼓励大家和StyleSheet搭配使用，这样可以使性能更好，代码看起来更清晰优美。当然内嵌样式如上面的例子也同样可以使用。但是估计大家使用StyleSheet的方法。从小养成好习惯很重要。

<!--more-->
### View属性
我们都知道，通过上篇文章flexbox的讲解，和flexbox搭配使用，可以使view做到横向布局还是垂直布局，还可以调整子元素和控件的位置。今天我们呢，再来扩展一下，看看还有那些属性可以使用呢？

* accessibilityLabel string 设置当用户与此元素交互时，“读屏器”（对视力障碍人士的辅助功能）阅读的文字。默认情况下，这个文字会通过遍历所有的子元素并累加所有的文本标签来构建。
* accessible bool 当为true时，表示该元素是可以进行访问，默认情况下所有可触摸的元素控件都是可以访问的。
* hitSlop {top: number, left: number, bottom: number, right: number} 功能是扩展可触摸的区域，比如你的控件大小是30x40的，你可以设置hitSlop的top为10，bottom为10，这样你的控件大小不变，但是可触摸点击的区域就变大了，为30x60了。
* onAccessibilityTap function 当accessible为true时，如果用户对一个已选中的无障碍元素做了一个双击手势时，系统会调用此函数。
* onLayout function 当组件挂载或者布局变化的时候调用，参数为：{nativeEvent: { layout: {x, y, width, height}}}
* onMagicTap function 当accessible为true时，如果用户做了一个双指轻触(Magic tap)手势，系统会回调此方法。
* 当然还有很多触摸响应的事件的回调，暂时先不讲，在运用到的时候再讲解即可，我先去研究透它。

### View的style
* Layout Props...
* Shadow Props...
* Transforms...
* backfaceVisibility 可选参数['visible', 'hidden']，视图显示还是隐藏
* backgroundColor color 背景颜色
* borderBottomColor color 底部边框颜色
* borderBottomLeftRadius 左下方圆角的大小
* borderBottomRightRadius 右下方圆角大小
* borderBottomWidth 底部边框的宽度
* borderColor color 边框颜色
* borderLeftColor color 左边框颜色
* borderLeftWidth 不翻译了，同上
* borderRadius 边框圆角
* borderRightColor color
* borderRightWidth ReactPropTypes.number
* borderStyle 可选参数['solid', 'dotted', 'dashed']实体线，点，虚线
* borderTopColor color
* borderTopLeftRadius ReactPropTypes.number
* borderTopRightRadius ReactPropTypes.number
* borderTopWidth ReactPropTypes.number
* borderWidth 边框宽度
* opacity 设置透明度
* overflow ReactPropTypes.oneOf(['visible', 'hidden'])
* elevation 设置立体阴影效果
  >这个参数是android独有的，相信学过android5.0的同学肯定认识它，z轴上显示阴影大小

### testID
* accessibilityComponentType android独有 是否该ui组件和原生组件一致化处理
* accessibilityLiveRegion enum('none', 'polite', 'assertive')  android 当ui发生变化时是否通知用户，只适用于API19以上的手机
* collapsable bool android 布局优化，如果一个View只用于布局它的子组件，则它可能会为了优化而从原生布局树中移除。 把此属性设为false可以禁用这个优化，以确保对应视图在原生结构中存在。
* importantForAccessibility [ 'auto', 'yes', 'no', 'no-hide-descendants'] android 设置视图响应等级
  可选的值:
  * auto - 系统来决定这个视图对于辅助功能是否重要 - 默认(推荐)。
  * yes - 这个视图对于辅助功能而言重要。
  * no - 这个视图对辅助功能不重要。
  * no-hide-descendants - 这个视图，以及所有的后代视图，都对于辅助功能不重要。
* needsOffscreenAlphaCompositing bool android 设置View是否需要渲染和半透明度效果处理的先后次序。
* renderToHardwareTextureAndroid bool android 决定这个视图是否要把它自己（以及所有的子视图）渲染到一个GPU上的硬件纹理中。
* accessibilityTraits PropTypes.oneOfType([ PropTypes.oneOf(AccessibilityTraits) ios 终于来了一个ios的，参数太多，自己去看吧，我对ios也不是很熟
* shouldRasterizeIOS bool ios 决定这个视图是否需要在被混合之前绘制到一个位图上。

官方文档地址：https://facebook.github.io/react-native/docs/view.html
里面有很多例子，自己去看，我就不复制粘贴过来占空间了，好学习的自行去阅读。

## Text
Text就是React Native中展示文本的一个组件，跟我们android中的TextView功能是一样的。学习它就没什么难度，所以就把Text和View放到一起来讲了，看起来很简单，这样一口气看完就可以了。

按官方文档的话来说，Text它也支持嵌套，样式和触摸处理，其实这句话我说不说，大家应该都能知道，不说又不好，说了，怕把大家当傻瓜，哈哈……这里由于Text太简单，例子就不先说了，直接讲属性。

### Text属性
* accessible bool 表明视图是否为可访问性元素，默认是true，可访问
* ellipsizeMode enum('head', 'middle', 'tail', 'clip') 这个功能相当于我们android中的ellipsize，文本很长时，省略号显示的位置，是开头，中间还是末尾显示省略号。
  >clip是ios上独有的，设置这个属性时，必须先设置text的行数。
* numberOfLines 文本的行数
* onLayout function 布局发生变化时调用
* onLongPress function 长按事件
* onPress function 按下或者点击事件

### Text的style
* color color 字体颜色
* fontFamily string 字体名称
* fontSize number 字体大小
* fontStyle ['normal', 'italic'] 字体样式，正常还是斜着
* fontWeight ['normal' /*default*/, 'bold', '100', '200', '300', '400', '500', '600', '700', '800', '900']  字体是正常，还是粗体，等等，数字代表粗细的权重
* lineHeight number 行高
* textAlign['auto' /*default*/, 'left', 'right', 'center', 'justify'] 指定文本的对齐方式。其中'justify'值仅iOS支持。
* textDecorationLine ['none' /*default*/, 'underline', 'line-through', 'underline line-through']  横线位置 
* textShadowColor color 阴影颜色
* textShadowOffset {width，height}  设置阴影效果
* textShadowRadius number 阴影效果圆角
* textAlignVertical ['auto' /*default*/, 'top', 'bottom', 'center'] 文本垂直对其方式
* ios独有的style
  * fontVariant 
  * letterSpacing
  * textDecorationColor color
  * textDecorationStyle 
  * writingDirection

### 特别内容
* 在Text内部的元素不再使用flexbox布局，而是采用文本布局。这意味着<Text>内部的元素不再是一个个矩形，而可能会在行末进行折叠。通俗点说：也就是一个Text接着Text，横向，如果文本已经到末尾了，那就直接换行。
* 而且style我们使用内嵌的方式，可以实现文本内容不同的样式，如果Text中又嵌入了一个Text，父Text文本是红色文字，子Text是蓝色，这样的内容我们可以实现红蓝一块展示的效果。
* 还有一个特点就是：样式继承，就是子组件如果没有写样式，会继承父组件的过来。

## 综合实例
大家思考一下，这个布局怎么实现，通过今天学习的，很简单的一个例子，如图：
![](https://raw.githubusercontent.com/loonggg/BlogImages/master/ReactNative/textview/1.png)

### 例子代码：
```js
import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View
} from 'react-native';

class TextViewDemo extends Component {
  render() {
    return (
      <View style={styles.container}>
        <View style={styles.title_view}>
         <Text style={styles.title_text}>
               新闻详情
         </Text>
        </View>
        <View style={styles.source_view}>
        <Text style={styles.source_text}>
               纽约时报中文网
         </Text>
         <Text style={styles.source_text}>
               四小时前
         </Text>
        </View>
         <Text style={styles.content_title_text}>
               与做爱相比，我们更重视接吻
         </Text>
         <Text style={styles.content_text}>
               为什么在热恋中我们总是吻个不停？为什么傲骨贤妻中的女主角与丈夫做爱的次数多于接吻？对用情专一人群的研究发现，接吻而非性爱的频率与爱情幸福度最密切相关。
         </Text>
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
    fontSize:22,
    textAlign:'center'
  },
  source_view:{
    flexDirection:'row',
    height:20,
    marginTop:10,
    justifyContent: 'space-between',
    alignItems: 'center',
    backgroundColor:'white',
  },
  source_text:{
    color:'#b1b1b1',
    fontSize:14,
    marginLeft:25,
    marginRight:25,
    textAlign:'center'
  },
  content_title_text:{
    color:'#343434',
    fontSize:20,
    marginTop:8,
    marginLeft:25,
    marginRight:25,
    textAlign:'left'
  },
  content_text:{
    color:'#b2b2b2',
    fontSize:16,
    marginTop:12,
    marginLeft:25,
    marginRight:25,
    textAlign:'left'
  },
});

AppRegistry.registerComponent('TextViewDemo', () => TextViewDemo);
```




