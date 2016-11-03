---
title: 基础篇章：React Native之Flexbox的讲解（Height and Width）
date: 2016-10-02 12:39:04
tags:
- React Native
- 基础组件
- Flexbox
categories: 
- 技术博客
- React Native
---
>今天在讲解Flexbox之前，我们先讲解一下高度和宽度的问题。因为Height and Width的问题很简单，就不单独写一篇文章了。顺带说一下即可。

## Height and Width
一个组件的高度和宽度，决定了它在屏幕上显示的大小。

## 固定尺寸
最简单的设置组件的尺寸的方法就是通过添加一个固定的宽度和高度。所有尺寸大小在React Native没有单位的,代表着独立的像素密度。

### 官网例子
```js
import React, { Component } from 'react';
import { AppRegistry, View } from 'react-native';

class FixedDimensionsBasics extends Component {
  render() {
    return (
      <View>
        <View style={{width: 50, height: 50, backgroundColor: 'powderblue'}} />
        <View style={{width: 100, height: 100, backgroundColor: 'skyblue'}} />
        <View style={{width: 150, height: 150, backgroundColor: 'steelblue'}} />
      </View>
    );
  }
};

AppRegistry.registerComponent('AwesomeProject', () => FixedDimensionsBasics);
```
<!--more-->
效果图：
![](https://raw.githubusercontent.com/loonggg/BlogImages/master/ReactNative/flexbox/1.png)

>这样设置尺寸大小的方式，比较适合于要求不同的屏幕上显示相同大小的View或者组件。写固定的尺寸大小，死值。

## 弹性宽高
我们可以在组件样式中使用flex让组件根据可用空间动态的收缩和扩展。通常情况下我们可以使用**flex: 1**，告诉某个组件来填充剩余的所有的空间，如果是多个组件的话，则是所有的这些组件去平分父容器中的剩余的所有空间。。如果这些并列的子组件的flex值不一样，则谁的值更大，谁占据剩余空间的比例就更大（跟我们android中weight的用法差不多）。

### 官网例子
```js
import React, { Component } from 'react';
import { AppRegistry, View } from 'react-native';

class FlexDimensionsBasics extends Component {
  render() {
    return (
      // Try removing the `flex: 1` on the parent View.
      // The parent will not have dimensions, so the children can't expand.
      // What if you add `height: 300` instead of `flex: 1`?
      <View style={{flex: 1}}>
        <View style={{flex: 1, backgroundColor: 'powderblue'}} />
        <View style={{flex: 2, backgroundColor: 'skyblue'}} />
        <View style={{flex: 3, backgroundColor: 'steelblue'}} />
      </View>
    );
  }
};

AppRegistry.registerComponent('AwesomeProject', () => FlexDimensionsBasics);
```
效果图：
![](https://raw.githubusercontent.com/loonggg/BlogImages/master/ReactNative/flexbox/2.png)

关于高度和宽度就讲这些吧，其实内容都是翻译与官网的docs，地址：
https://facebook.github.io/react-native/docs/height-and-width.html#height-and-width

## Flexbox
一个组件可以使用Flexbox指定其子组件或元素之间的布局。Flexbox旨在为不同的屏幕上提供一致的布局。

通常情况下，我们结合使用flexDirection、alignItems和 justifyContent三个样式属性就已经能够实现我们所需的布局。

>注意：Flexbox在React Native的工作原理和使用方式与css在web上的方式基本一样，当然也有一些例外：比如flexDirection的默认值是column而不是row，alignItems的默认值是stretch而不是flex-start，以及flex只能指定一个数字值。

### Flex Direction
向一个组件的样式中添加Flex Direction可以决定一个布局的主轴。子元素应该沿着水平方向(row)排列，还是沿着竖直方向(column)排列呢？默认值是竖直(column)方向。
#### 官网例子
```js
import React, { Component } from 'react';
import { AppRegistry, View } from 'react-native';

class FlexDirectionBasics extends Component {
  render() {
    return (
      // Try setting `flexDirection` to `column`.
      <View style={{flex: 1, flexDirection: 'row'}}>
        <View style={{width: 50, height: 50, backgroundColor: 'powderblue'}} />
        <View style={{width: 50, height: 50, backgroundColor: 'skyblue'}} />
        <View style={{width: 50, height: 50, backgroundColor: 'steelblue'}} />
      </View>
    );
  }
};

AppRegistry.registerComponent('AwesomeProject', () => FlexDirectionBasics);
```
效果图：
![](https://raw.githubusercontent.com/loonggg/BlogImages/master/ReactNative/flexbox/3.png)

### Justify Content 
向组件的样式中添加Justify Content可以决定其子元素沿着主轴的排列方式。子元素应该分布在主轴的开始端，还是中间，最后，还是均匀分布？可用的选项有：flex-start、center、flex-end、space-around以及space-between。

* flex-start：弹性盒子元素将与行起始位置对齐。该行的第一个子元素的主起始位置的边界将与该行的主起始位置的边界对齐，同时所有后续的伸缩盒项目与其前一个项目对齐。
* flex-end：弹性盒子元素将与行结束位置对齐。该行的第一个子元素的主结束位置的边界将与该行的主结束位置的边界对齐，同时所有后续的伸缩盒项目与其前一个项目对齐。
* center：弹性盒子元素将与行中间位置对齐。该行的子元素将相互对齐并在行中居中对齐，同时第一个元素与行的主起始位置的边距等同与最后一个元素与行的主结束位置的边距（如果剩余空间是负数，则保持两端相等长度的溢出）。
* space-between：弹性盒子元素会均匀分布在行里。如果最左边的剩余空间是负数，或该行只有一个子元素，则该值等效于'flex-start'。在其它情况下，第一个元素的边界与行的主起始位置的边界对齐，同时最后一个元素的边界与行的主结束位置的边距对齐，而剩余的伸缩盒项目则平均分布，并确保两两之间的空白空间相等。
* space-around：弹性盒子元素会均匀分布在行里，两端保留子元素与子元素之间间距大小的一半。如果最左边的剩余空间是负数，或该行只有一个伸缩盒项目，则该值等效于'center'。在其它情况下，伸缩盒项目则平均分布，并确保两两之间的空白空间相等，同时第一个元素前的空间以及最后一个元素后的空间为其他空白空间的一半。

#### 官网例子
```js
import React, { Component } from 'react';
import { AppRegistry, View } from 'react-native';

class JustifyContentBasics extends Component {
  render() {
    return (
      // Try setting `justifyContent` to `center`.
      // Try setting `flexDirection` to `row`.
      <View style={{
        flex: 1,
        flexDirection: 'column',
        justifyContent: 'space-between',
      }}>
        <View style={{width: 50, height: 50, backgroundColor: 'powderblue'}} />
        <View style={{width: 50, height: 50, backgroundColor: 'skyblue'}} />
        <View style={{width: 50, height: 50, backgroundColor: 'steelblue'}} />
      </View>
    );
  }
};

AppRegistry.registerComponent('AwesomeProject', () => JustifyContentBasics);
```
效果图：
![](https://raw.githubusercontent.com/loonggg/BlogImages/master/ReactNative/flexbox/4.png)

### Align Items
向组件的样式（style）中添加alignItems可以决定其子元素沿着次轴（就是与主轴垂直的轴，比如若主轴方向为row，则次轴方向为column）的排列方式。子元素是应该靠近次轴的开始端还是中间，还是末端，亦或是拉伸来填补呢？可用选项有：flex-start、center、flex-end以及stretch。

* flex-start：弹性盒子元素的次轴起始位置的边界紧靠该行的次轴起始边界。
* flex-end：弹性盒子元素的次轴起始位置的边界紧靠住该行的次轴结束边界。
* center：弹性盒子元素在该行的次轴）上居中放置。（如果该行的尺寸小于弹性盒子元素的尺寸，则会向两个方向溢出相同的长度）。
* stretch：如果指定次轴大小的属性值为'auto'，则其值会使项目的边距盒的尺寸尽可能接近所在行的尺寸，但同时会遵照'min/max-width/height'属性的限制。

>注意：要使stretch选项生效的话，子元素在次轴方向上不能有固定的尺寸。在下面的例子中：只有将子元素样式中的width: 50去掉之后，alignItems: 'stretch'才能生效。

#### 官网例子
```js
import React, { Component } from 'react';
import { AppRegistry, View } from 'react-native';

class AlignItemsBasics extends Component {
  render() {
    return (
      // Try setting `alignItems` to 'flex-start'
      // Try setting `justifyContent` to `flex-end`.
      // Try setting `flexDirection` to `row`.
      <View style={{
        flex: 1,
        flexDirection: 'column',
        justifyContent: 'center',
        alignItems: 'center',
      }}>
        <View style={{width: 50, height: 50, backgroundColor: 'powderblue'}} />
        <View style={{width: 50, height: 50, backgroundColor: 'skyblue'}} />
        <View style={{width: 50, height: 50, backgroundColor: 'steelblue'}} />
      </View>
    );
  }
};

AppRegistry.registerComponent('AwesomeProject', () => AlignItemsBasics);
```
效果图：
![](https://raw.githubusercontent.com/loonggg/BlogImages/master/ReactNative/flexbox/5.png)

文章翻译并参考于Flexbox官方文档，地址：
https://facebook.github.io/react-native/docs/flexbox.html

好了，到这里关于Flexbox的讲解就讲到这里了，关于Flexbox运用，上面的例子只是冰山一角，要想真正熟练掌握，还得靠自己亲自动手去写，去实践，才能够真正来理解各个属性的意思。赶紧动手去实践吧。

