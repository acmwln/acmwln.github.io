---
title: CRN布局样式中的一些小分享
date: 2020-03-10 14:54:07
categories: React Native
tags: React Native
---



1.	关于`<textInput>`输入框： `TextInput`在安卓上默认有一个底边黑线框，同时会有一些`padding`。如果要想使其看起来和`iOS`上尽量一致，则需要设置`padding: 0`，同时设置`underlineColorAndroid="transparent"`来去掉底边框。加上`padding: 0 `保证不同机型上下垂直居中。
2.	关于省略号： crn中是通过`ellipsizeMode='tail' numberOfLines={1}`两个属性同时控制省略号…（注：这两个属性需要写在`<Text>`上，不能写在`style`里。）
3.	`borderRadius`不能和实现三角形样式一起使用，否则不生效；单独写`borderBottomLeftRadius`和`borderBottomRightRadius、borderTopLeftRadius、borderTopRightRadius`也不会生效。
4.	实现文本垂直居中的时候尽量不要使用行高，因为行高会在不同平台和机型上面表现的形式不一样，为了保险起见，根据主轴方向使用`justifyContent: ‘center’`就可以啦!
5.	一般设计稿的尺寸是`iphone6`的尺寸`750px`的，我们需要做个适配：
```javascript
const { width, height } = Dimensions.get('window');
let WIDTH = width, HEIGHT = height;
const rem = (a) => { return a * (WIDTH / 750); };
```
6.	关于`<Text>`： `Text`元素会继承`View`层的`background`: 可以给`Text`设置`backgroundColor:'transparent'`解决
7.	`crn`中的`width`和`height`是包含`padding`和`border`的。
8.	关于`Image`：可以做背景图展示，它有几个属性方法：
`Source：{uri:string}` 进行标记图片的引用，该参数可以为一个网络url地址或者一个本地的路径
resizeMode：缩放比例，可选参数`('cover', 'contain', 'stretch')` 该当图片的尺寸超过布局的尺寸的时候，会根据设置`Mode`进行缩放或者裁剪图片，eg：`resizeMode: Image.resizeMode.cover`
  
9.	关于雪碧图`<SpriteImage>`： 雪碧图需要去根据crop裁剪图片，所以会导致在不同机型或者平台中有拉伸缩小情况。以后建议尽量不适用雪碧图。如果需要使用雪碧图也有解决方案: 通过判断机型的dpr来加载图片，安卓的dpr比较散，有1、1.5、2、3、4的，ios的dpr有2、3的。所以需要@3图和@2倍图来做下兼容：
```javascript
<SpriteImage style={styles.spritePic}
source={{ uri: PixelRatio.get() === 3 ? 'https://pic.c-ctrip.com/VacationH5Pic/taocan/sdp/n_banner/un_banner@3.png' : 'https://pic.c-ctrip.com/VacationH5Pic/taocan/sdp/n_banner/un_banner.png', imageSize: { width: 169, height: 167 }, crop }}
>
</SpriteImage>
```

10.	对于小屏手机例如`320`的，可以通过`width === 320` 来进行小屏手机的一些兼容
11.	针对`1px`的`border`线: `border: 1 / PixelRatio.get()`
12.	关于`<TouchableOpacity>`：添加点击事件的标签，展示效果会有透明度的变化可以使用`activeOpacity={1}`去除透明度的变化，`<View>`外层套此标签的时候style样式尽量写在`<View>`里，之前做的时候发现写在
`<TouchableOpacity>`这个里面出现问题。

13. `crn`中的分辨率适配： `react native`中使用的尺寸单位是`pt`,是一个绝对长度，设计稿使用的是`px`，这两种尺寸换算可以通过`PixelRatio`转换。
```javascript
import {PixelRatio} from 'react-native';
const pt2px = pt=>PixelRatio.getPixelSizeForLayoutSize(pt);
const px2pt = px=>PixelRatio.roundToNearestPixel(px);
```

14. 梯形+圆角
```javascript
import React, { Component } from 'react';
import { Text, View, StyleSheet } from 'react-native';

export default class App extends Component {
  render() {
    return (
      <View style={styles.container}>
          <View style = {[styles.btn_one,{backgroundColor: '#fff'}]}>
            <Text style = {styles.textblock}>高铁+地铁</Text>
            <View style = {styles.cateory}></View>
          </View>
          <View style = {styles.btn_one}>
            <Text style = {styles.textblock}>高铁</Text>
          </View>
           <View style = {styles.btn_one}>
            <Text style = {styles.textblock}>地铁</Text>
          </View>
      </View>
    );
  }
}
const styles = StyleSheet.create({
  container: {
    backgroundColor: '#53b8fb',
    height: 100,
    flexDirection: "row",
    borderTopLeftRadius: 7,
    borderTopRightRadius: 7,
    marginTop: 100,
  },
  btn_one: {
    flex: 1,
    borderRadius: 10,
  },

cateory: {
 height:0,
 width:0,
 borderBottomWidth: 100,
 borderLeftWidth: 50,
 borderRightWidth: 50,
 borderStyle: 'solid',
 borderBottomColor: '#f00',
 borderLeftColor: 'transparent',
 borderRightColor: 'transparent',
 marginRight: 10,
 transform: [{skewX: '45deg'},{rotateX:'180deg'},{rotateY:'180deg'}],
 position: 'absolute',
 bottom: 0,
 left: 0,
 },
textblock: {
    color: '#fff',
    fontSize: 14,
  }
});
```

