---
title: 精读React新特性
date: 2020-03-22 14:54:07
categories: React
tags: React
---

## 序
于 2017.09.26 Facebook 发布 React v16.0 版本，时至今日已更新到 React v16.6，且引入了大量的令人振奋的新特性，本文章将带领大家根据 React 更新的时间脉络了解 React16 的新特性。
按照 React16 的更新时间，从 React v16.0 ~ React v16.6 进行概述。

## React v16.0
```bash
render 支持返回数组和字符串、Error Boundaries、createPortal、支持自定义 DOM 属性、减少文件体积、fiber；
```

## React v16.1
```bash
react-call-return；
```

## React v16.2
```bash
Fragment；
```

## React v16.3
```bash
createContext、createRef、forwardRef、生命周期函数的更新、Strict Mode；
```

## React v16.4
```
Pointer Events、update getDerivedStateFromProps；
```

## React v16.5
```bash
Profiler；
```

## React v16.6
```bash
memo、lazy、Suspense、static contextType、static getDerivedStateFromError()；

```
## React v16.7（~Q1 2019）
```bash
Hooks；
```

## React v16.8（~Q2 2019）
```bash
Concurrent Rendering；
```
## React v16.9（~mid 2019）
```bash
Suspense for Data Fetching；
```



## 新的生命周期

### Mounting（加载阶段：涉及4个钩子函数）

###### constructor()
```bash
加载的时候调用一次，可以初始化state
```

###### static getDerivedStateFromProps(props, state)
```bash
组件每次被rerender的时候，包括在组件构建之后
(虚拟dom之后，实际dom挂载之前)，每次获取新的props或state之后；
每次接收新的props之后都会返回一个对象作为
新的state，返回null则说明不需要更新state；配合componentDidUpdate，
可以覆盖componentWillReceiveProps的所有用法
```


`getDerivedStateFromProps(nextProps, prevState)` 其作用是根据传递的 `props` 来更新 `state`。它的一大特点是无副作用，由于处在 `Render Phase` 阶段，所以在每次的更新都会触发该函数， 在 API 设计上采用了静态方法，使其无法访问实例、无法通过 `ref` 访问到 `DOM` 对象等，保证了该函数的纯粹高效。

为了配合未来的 `React` 异步渲染机制，`React v16.4` 对 `getDerivedStateFromProps` 做了一些改变， 使其不仅在 `props` 更新时会被调用，`setState` 时也会被触发。

- 如果改变 `props` 的同时，有副作用的产生，这时应该使用 `componentDidUpdate`；
- 如果想要根据 `props` 计算属性，应该考虑将结果 `memoization` 化；
- 如果想要根据 `props` 变化来重置某些状态，应该考虑使用 受控组件；

```javascript
static getDerivedStateFromProps(props, state) {
  if (props.value !== state.controlledValue) {
    return {
      controlledValue: props.value,
    };
  }
  
  return null;
}
```

###### render()
```bash 
react最重要的步骤，创建虚拟dom，进行diff算法，更新dom树都在此进行
```

###### componentDidMount()
```bash
组件渲染之后调用，只调用一次
```

### Updating（更新阶段：涉及5个钩子函数)

###### static getDerivedStateFromProps(props, state)
```bash 
组件每次被rerender的时候，包括在组件构建之后
(虚拟dom之后，实际dom挂载之前)，每次获取新的props或state之后；
每次接收新的props之后都会返回一个对象
作为新的state，返回null则说明不需要更新state；配合componentDidUpdate，
可以覆盖componentWillReceiveProps的所有用法
```

###### shouldComponentUpdate(nextProps, nextState)
```bash
组件接收到新的props或者state时调用，
return true就会更新dom（使用diff算法更新），
return false能阻止更新（不调用render）
```

###### render()
```bash 
react最重要的步骤，创建虚拟dom，进行diff算法，更新dom树都在此进行
```

###### getSnapshotBeforeUpdate(prevProps, prevState)
```bash
触发时间: 会在组件更新之前获取一个snapshot
（update发生的时候，在render之后，在组件dom渲染之前），
并可以将计算得到的值或从DOM得到的信息传递到
componentDidUpdate(prevProps,prevState,snapshot)函数的第三个参数，
配合componentDidUpdate, 可以覆盖componentWillUpdate的所有用法，
常用于scroll位置定位等场景。
```

###### componentDidUpdate()
```bash 
组件加载时不调用，组件更新完成后调用
```


### Unmounting（卸载阶段：涉及1个钩子函数）

###### componentWillUnmount
```bash
组件渲染之后调用，只调用一次
```


### Error Handling(错误处理)

###### componentDidCatch(error，info)
```bash 
任何一处的javascript报错会触发
```


