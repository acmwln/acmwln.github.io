---
title: CRN踩坑记
date: 2020-03-10 14:06:52
categories: React Native
tags: 
    - React Native
---


## 非技术方面
### 遇到的坑：
	1.	需求问题，由于多数需求与以前保持一致也没有出新的需求文档，就直接扒了h5的逻辑，提测后就哭了:sob:
	2.	对CRN现有功能不熟悉，例：右上角消息盒子，到开发阶段后期才发现 公共不支持此功能
### 解决办法：
	1.	测试让咋改就咋改了。。。以后做之前一定要明确需求，不能自己YY，即使是扒逻辑也要和相关人员进行确认
	2.	和框架沟通支持相关功能。做之前熟悉下相关API，对不确定是否能做到的东西要尽早提出。

## 技术方面

### 一. 功能
熟悉RN及CRN的API后基本没什么大问题

### 二. 性能

#### 页面切换
```javascript
constructor(props) {
    super(props)
    this.state = {
        showInit: true
    }
}
componentDidMount() {
    InteractionManager.runAfterInteractions(() => {
        this.setState({
            showInit: false
        })
    })
}
render() {
    {this.state.showInit ?
    <View><LoadingView style={{ flex: 1 }} /></View>:
    <ViewPort>
        <HeaderView page={this} title={'选择日期'} />
        <View>巴拉巴拉</View>
    </ViewPort>
    }
}
componentDidMount() {
    const { dir = 'flight', type,index,curCity = {} } = this.props
    InteractionManager.runAfterInteractions(()=>{
        this.props.events.reqCityList({
            dir,
            type,
            index,
            curCity
        })
    })
}
```
耗时较长的工作安排到所有互动或动画完成之后再进行 `RN InteractionManager`
tip:注意`dispatch`用的点，`dispatch`会改变组件的`state`

#### 列表渲染

#####  1. `ScrollView`
```bash
子元素一次性渲染 
scrollTo({ x: 0, animated: true })  animated为true时对x方向定义点问题  
tip：安卓上scrollTo方法需要添加setTimeout才生效。。。
```

##### 2. `ListView`
可惰性渲染，超出页面的不会回收，数据量大的时候，内存损耗大  
eg：城市选择页，有右边索引条，快速滑动。
坑：数据量大，直接滚动到最下面的时候，`ios`为白屏，安卓只能滚到当前渲染到的地方。
```bash
解决方法：
1. onContentSizeChange
监控当前渲染的位置，没有到要滚到点的位置，自动触发scrollTo再次滚
2. scrollRenderAheadDistance
当一个行接近屏幕范围多少像素之内的时候，就开始渲染这一行。
```
```javascript
<ListView
    ref={listView => this._listView = listView}
    dataSource={this.state.dataSource}
    onContentSizeChange={this.onContentSizeChange.bind(this)}
    renderRow={this.renderRow.bind(this)}
    renderSectionHeader={this.renderSectionHeader}
    initialListSize={20}//组件刚挂载的时候渲染多少行数据
    scrollRenderAheadDistance={this.state.height}
    pageSize={100} //每次事件循环（每帧）渲染的行数
    />
```


##### 3. `CRNListView`
基于`ListView`
有一些自定义的功能，下拉刷新、加载更多、侧边栏等
但是侧边栏支持ios不支持安卓。。。:triumph:
`SectionList FlatList`
官方为解决`listview`性能问题重新推出的`list`
有回收机制（不再屏幕内的内容会被回收）
```javascript
<FlatList
    initialNumToRender={6}//如方法名，这部分不会在滚动过程中被卸载
    maxToRenderPerBatch={6}
    getItemLayout={this.getItemLayout}//可选的优化 对于我们的场景（有scrollTo）必需
    horizontal={this.props.horizontal}
    keyExtractor={(item)=>item.showMonth}
    ref={flatList => this._flatList = flatList}
    data={this.state.monthInfo}
    renderItem={this.getMonthView.bind(this)}/>
```
属性 `initialScrollIndex ` 
    小坑：设置了这个属性`（getItemLayout(data, index)`这个`index`会返回-1）
方法 `scrollToIndex scrollToOffset`

#### 代码写法
- 减少`View`层的嵌套，`cpu`优化
- 避免不必要的循环，尤其是在页面渲染的时候（反面例子 日历页计算今天明天后天的时候）
- 健壮性 慎用`a.b.c.d`写法；`async await` 这种写法不同与`promise`的`then catch`，需要用`try catch`捕获异常；
- 可读性
- 可维护性

#### 其它
- `console` 会影响性能
- `debugger`页面要单拉出来

### 三. 样式
- 遵循标准UI稿或者`CSS`稿
- 尽量少使用特殊判断或者计算，能用`flex`解决的要用`flex`解决
- 有意识的考虑到不同屏幕手机
- 慎用 `Sprite Image`，会增大不必要的内存消耗。

### 四. 兼容性
- 样式的兼容
- 注意`API`上 `android ios`的不同点

### 五. 其他
- 调试 `debugger console.log`
- 性能调试 `console.time`

## git提交事项
- 提测后 慎提交`mock`数据
- 尽量用`rebase`
- 切记不要影响别人的项目代码

## 最后
会深入学习会在性能及用户体验上多下功夫，发挥`RN`的优势




> 对自己的代码负责。
> 没有解决不了的bug。如果有，一定是没有找到方法。
> 没有填不平的坑。如果有，一定要尽力填平。