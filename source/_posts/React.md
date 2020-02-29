---
title: React的那些事儿
date: 2019-10-04 16:28:07
categories: 前端
tags: 前端
---

## 前言
React的一些核心知识点，希望通过这篇文章好好总结下

<h5> 1. React 生命周期有哪些，16版本生命周期发生了哪些变化？</h5>
<table width="100%">
   <tr>
      <td colspan="4" style="text-align:center">15生命周期</td>
   </tr>
   <tr>
      <td>初始化阶段</td>
      <td>挂载阶段</td>
      <td width="35%">更新阶段</td>
      <td>卸载阶段</td>
   </tr>
   <tr>
      <td>constructor 构造函数</td>
      <td>componentWillMount 组件初始化渲染前调用</td>
      <td width="35%">componentWillReceiveProps  组件将要接收新props前调用</td>
      <td>componentWillUnmount 组件卸载前调用</td>
   </tr>
   <tr>
      <td>getDefaultProps   props默认值</td>
      <td>render 组件渲染</td>
      <td width="35%">shouldComponentUpdate 组件是否需要更新</td>
      <td></td>
   </tr>
   <tr>
      <td>getInitialState       state 默认值</td>
      <td>componentDidMount 组件挂载到 DOM 后调用</td>
      <td width="35%">componentWillUpdate 组件更新前调用</td>
      <td></td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td>render 组件渲染</td>
      <td></td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td>componentDidUpdate 组件更新后调用</td>
      <td></td>
   </tr>
</table>


<table>
   <tr>
      <td colspan="5" style="text-align:center">16生命周期</td>
   </tr>
   <tr>
      <td>初始化阶段</td>
      <td>挂载阶段</td>
      <td>更新阶段</td>
      <td>卸载阶段</td>
      <td>错误处理</td>
   </tr>
   <tr>
      <td>constructor 构造函数</td>
      <td>
      staticgetDerived
      StateFromProps
      (props,state)
      </td>
      <td>
      staticgetDerived
      StateFromProps
      (props,state)</td>
      <td>componentWillUnmount 组件卸载前调用</td>
      <td>componentDidCatch</td>
   </tr>
   <tr>
      <td>getDefaultProps   props默认值</td>
      <td>render 组件渲染</td>
      <td>shouldComponentUpdate 组件是否需要更新</td>
      <td></td>
      <td></td>
   </tr>
   <tr>
      <td>getInitialState       state 默认值</td>
      <td>componentDidMount 组件挂载到 DOM 后调用</td>
      <td>getSnapshotBeforeUpdate(prevProps,prevState)</td>
      <td></td>
      <td></td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td>render 组件渲染</td>
      <td></td>
      <td></td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td>componentDidUpdate 组件更新后调用</td>
      <td></td>
      <td></td>
   </tr>
</table>

+ getDerivedStateFromProps:组件每次被 rerender 的时候，包括在组件构建之后(虚拟 dom 之后，实际 dom 挂载之前)，每次获取新的 props 或 state 之后；每次接收新的props之后都会返回一个对象作为新的 state ，返回null则说明不需要更新state;配合componentDidUpdate,可以覆盖 componentWillReceiveProps 的所有用法

+ getSnapshotBeforeUpdate:触发时间: update 发生的时候，在 render 之后，在组件 dom 渲染之前；返回一个值，作为 componentDidUpdate 的第三个参数；配合 componentDidUpdate , 可以覆盖 componentWillUpdate 的所有用法

+ React16 新的生命周期弃用了 componentWillMount、componentWillReceivePorps，componentWillUpdate 新增了 getDerivedStateFromProps、getSnapshotBeforeUpdate 来代替弃用的三个钩子函数。

+ React16 并没有删除这三个钩子函数，但是不能和新增的钩子函数混用,React17 将会删除这三个钩子函数，新增了对错误的处理（ componentDidCatch ）

<h5> 2. setState是同步的还是异步的？</h5>

* 生命周期和合成事件

在 React 的生命周期和合成事件中,React仍然处于他的更新机制中,这时无论调用多少次 setState,都不会立即执行更新,而是将要更新的内容存入 _pendingStateQueue,将要更新的组件存入dirtyComponent。

当上一次更新机制执行完毕,以生命周期为例,所有组件,即最顶层组件didmount后会将批处理标志设置为false。这时将取出dirtyComponent中的组件以及 _pendingStateQueue 中的 state 进行更新。这样就可以确保组件不会被重新渲染多次。

所以,如上面的代码,当我们在执行setState后立即去获取state,这时是获取不到更新后的state的,因为处于React的批处理机制中,state被暂存起来,待批处理机制完成之后,统一进行更新。

所以,setState本身并不是异步的,而是React的批处理机制给人一种异步的假象。

* 异步代码和原生事件中

如上面的代码,当我们在异步代码中调用setState时,根据 JavaScript 的异步机制,会将异步代码先暂存,等所有同步代码执行完毕后在执行,这时React的批处理机制已经走完,处理标志设被设置为false,这时再调用setState 即可立即执行更新，拿到更新后的结果。

在原生事件中调用setState并不会出发React的批处理机制,所以立即能拿到最新结果。

最佳实践

setState的第二个参数接收一个函数，该函数会在React的批处理机制完成之后调用,所以你想在调用setState后立即获取更新后的值,请在该回调函数中获取。

[由实际问题探究setState的执行机制](https://mp.weixin.qq.com/s?__biz=Mzg2NDAzMjE5NQ==&mid=2247483989&idx=1&sn=d78f889c6e1d7d57058c9c232b1a620e&chksm=ce6ec6f9f9194fef681c79ee869bf58d5413132c73496710b2eb32c859a2249a895c2ce8a7cd&scene=21#wechat_redirect)

* 为什么有时连续多次setState只有一次生效？


原因就是React 会批处理机制中存储的多个setState进行合并,来看下React源码中的 _assign 函数,类似于 Object的 assign:

如果传入的是对象,很明显会被合并成一次,所以上面的代码两次打印的结果是相同的:

注意,assign 函数中对函数做了特殊处理,处理第一个参数传入的是函数,函数的参数 preState 是前一次合并后的结果，所以计算结果是准确的：

最佳实践

React会对多次连续的 setState 进行合并，如果你想立即使用上次 setState 后的结果进行下一次 setState,可以让 setState 接收一个函数而不是一个对象。这个函数用上一个 state 作为第一个参数,将此次更新被应用时的 props 做为第二个参数。





