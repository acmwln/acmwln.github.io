---
title: 框架/类库的比较
date: 2020-03-10 15:35:03
categories: 前端
tags: 前端
---

## 序
你使用过哪些前端框架/类库，其各有什么优缺点？

### 1. React
#### 优点：
 1. 速度快：通过在vdom的操作实现真实dom的局部更新
 2. 跨浏览器兼容
 3. 模块化
 4. 单向数据流
 5. 同构,接近原生的js
 6. 和打包工具配合使用兼容性好

#### 缺点：
React本身知识一个v而已，并不是一个完整的框架，如果大型项目中如果需要一套完整的框架的话需要搭配react-router使用。


### 2. Vue
是一个构建数据驱动的web界面的渐进式MVVM框架
#### 优点：
 1. 性能好
 2. 简单易用
 3. 前后端分离
 4. 单页面应用用户体验好
 5. 组件化
 6. MVVM

#### 缺点：
1. 不适于seo优化，而且封装的比较厉害，报错不明显，适合单人开发，适合中小型项目
2. 导航不可用，如果一定要导航需要自行实现前进、后退。（由于是单页面不能用浏览器的前进后退功能，所以需要自己建立堆栈管理）。
3. 初次加载时耗时多。
4. 页面复杂度提高很多。


### 3. Angular
#### 优点：
 1. 模块功能强大丰富，自带了极其丰富的angular指令
 2. 是一个比较完善的前端框架，包含服务，模版，数据双向绑定，模块化，路由等。。。
 3. 可以自定义指令，可以在项目中多次使用

#### 缺点：
1. 入门很容易，深入学习后概念很多，较难理解
2. 对IE6/7兼容性不好
3. 很灵活，指令应用最佳实践教程少

### 4. Jquery
#### 优点：
 1. 代码简洁
 2. 脚本与页面分离
 3. 丰富的插件开发
 4. 性能支持好
 5. 节约学习成本
 6. 是各大厂商使用类库的标准

#### 缺点：
1. 不能向后兼容，不能兼容早期版本，早期的插件不可用
2. 同一个页面不能使用多个插件，会有冲突
3. 版本更新较快，不稳定


### 5. Nodejs
#### 优点：
 1. 高并发
 2. 适合I/O密集型应用

#### 缺点：
1. 不适合CPU密集型应用
2. 只支持单核CPU
3. 可靠性低，一旦代码某个环节崩溃，整个系统都崩溃
4. 更新快
5. Debug不方便，错误没有stack trace


### 6. Express
#### 优点：
 历史悠久，文档资料多并且完整，容易学习

#### 缺点：
callback比较多

### 7. lodash

#### 优点：
 1. 内部封装了很多字符串，数组，对象等常见数据类型的处理函数
 2. 不是在原有数据上进行操作，而是复制出一份新的数据而不改变原有数据

#### 缺点：
使用lodash库偏离了原生js的写法

8.9.。。。。。后续继续补充。。。

## React和Vue的区别与共同点

#### 共同点:
1. 都支持组件化
2. 都是数据驱动视图

#### 区别

##### 本质区别:
1. Vue本质是MVVM框架，由MVC发展而来
2. React本质上是前端组件化框架，由后端组件化发展而来

##### 模版区别:
1. Vue使用模版(最初由Angular提出)
2. React使用JSX
3. 模版语法上，更加倾向于JSX
4. 模版分离上，更加倾向于Vue

##### 组件化区别:
1. React本身就是组件化，没有组件化就不是React
2. Vue也支持组件化，不过是在MVVM上的扩展
3. 查阅Vue组件化的文档有很多（侧面反应）

## 总结

1. 国内使用，首推Vue，文档更易读，易学，社区够大
2. 如果团队水平较高，推荐使用React，组件化和JSX


