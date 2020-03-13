---
title: 浅谈cookie，session和localStorage，sessionStorage的区别及cookie安全方面
date: 2020-03-02 10:47:14
categories: 前端
tags:
    - 前端
---

## 序
浅谈`cookie`，`session`和`localStorage`，`sessionStorage`的区别，既然是浅谈，就不会详细从底层原理解释这几个的区别，就简单地聊一下，这几个的区别，优缺点，应用场景

## 介绍

浏览器的缓存机制提供了可以将用户数据存储在客户端上的方式，可以利用`cookie`和`session`跟服务端进行数据交互。
`cookie`和`session`都是用来跟踪浏览器用户身份的会话方式，`cookie`通过在客户端记录信息确定用户身份；session通过在服务器端记录信息确定用户身份，但是`session`的实现依赖于`cookie`。`sessionId`（`session`的唯一标识需要存放在客户端）
## cookie和session的区别

### 1.使用方式

##### cookie机制：
- 不设置过期时间的`cookie`，存储在内存中，生命周期在会话期间，关闭浏览器窗口，`cookie`就消失。称为会话`cookie`
- 设置了过期时间的cookie，会在硬盘中，关闭了浏览器再次打开还是会读取`cookie`，只要不超过时间
- cookie是服务端发给客户端的特殊信息，`cookie`是以文本的方式保存在客户端，每次请求时都带上它

##### session机制：
当服务器收到请求需要创建`session`对象时，首先会检查客户端请求中是否包含`sessionid`。如果有`sessionid`，服务器将根据该id返回对应`session`对象。如果客户端请求中没有`sessionid`，服务器会创建新的`session`对象，并把`sessionid`在本次响应中返回给客户端。通常使用cookie方式存储sessionid到客户端，在交互中浏览器按照规则将`sessionid`发送给服务器。如果用户禁用`cookie`，则要使用`URL`重写，可以通过`response.encodeURL(url)`进行实现；`API`对`encodeURL`的解释为，当浏览器支持`cookie`时，`url`不做任何处理；当浏览器不支持`cookie`的时候，将会重写`URL`将`sessionid`拼接到访问地址后。

### 2.保持状态
`cookie`保存在浏览器端
`session`保存在服务器端

### 3.存储的大小
单个`cookie`保存的数据不能超过`4kb`；很多浏览器都限制一个站点最多保存20个`cookie`。
`session`大小没有限制。

### 4.存储内容
`cookie`只能保存字符串类型，以文本的方式。
`session`通过类似与`Hashtable`的数据结构来保存，能支持任何类型的对象（`session`中可含有多个对象）

### 5.安全性
`session`的安全性大于`cookie`。原因如下：
- `sessionid`存储在`cookie`中，若要攻破`session`首先要攻破`cookie`；
- `sessionid`是要有人登录，或者启动`session_start`才会有，所以攻破`cookie`也不一定能得到`sessionid`；
- 第二次启动`session_start`后，前一次的`sessionid`就是失效了，`session`过期后，`sessionid`也随之失效。
- `sessionid`是加密的。
- `cookie`不是很安全，别人可以分析存放在本地的`cookie`并进行`cookie`欺骗，考虑到安全应当使用`session`
综上所述，攻击者必须在短时间内攻破加密的`sessionid`，这很难。

### 6.性能
`session`会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能，考虑到减轻服务器性能方面，应当使用`cookie`。

### 7.应用场景
##### cookie`:
- 判断用户是否登录过网站，以便下次登录时能够实现自动登录（或者记住密码）。
- 保存上次登录的时间等信息。
- 保存上次查看的页面
- 浏览计数

##### session：
- 网上商城中的购物车：添加了商品之后客户端处可以知道添加了哪些商品，而服务器端如何判别呢，所以也需要存储一些信息就用到了`session`
- 保存用户登录信息
- 将某些数据放入`session`中，供同一用户的不同页面使用
- 防止用户非法登录
- 购物车最好使用`cookie`，但是`cookie`是可以在客户端禁用的，这时候我们要使用`cookie+`数据库的方式实现，当从`cookie`中不能取出数据时，就从数据库获取。

### 8.缺点
##### cookie：
- 大小受限
- 用户可以操作（禁用）cookie，使功能受限
- 安全性较低
- 有些状态不可能保存在客户端
- 每次访问都要传送cookie给服务器，浪费宽带
- cookie数据有路径（path）的概念，可以限制cookie只属于某个路径下。

##### session：
- `session`保存的东西越多，就越占用服务器内存，对于用户在线人数较多的网站，服务器的内存压力会比较大
- 依赖于`cookie`（`sessionid`保存在`cookie`），如果禁用`cookie`，则要使用URL重写
- 创建`session`变量有很大的随意性，可随时调用，不需要开发者做精确地处理，所以过度的使用`session`变量将会导致代码不可读而且不好维护。

## cookie和session的相同点
- `session`和`cookie`都是由服务器生成的
- `session`和`cookie`都是键值对，即`session`和`cookie`就是用来保存特定的值的一种技术


##  cookie机制(一个cookie的设置以及发送过程分为以下四步)：
- 客户端发送一个`http`请求到服务器端
- 服务器端发送一个`http`响应到客户端，其中包含`Set-Cookie`头部
- 客户端发送一个`http`请求到服务器端，其中包含`Cookie`头部
- 服务器端发送一个`http`响应到客户端

## cookie属性项
<table width="100%">
   <tr>
      <td>属性项</td>
      <td>属性项介绍</td>
   </tr>
   <tr>
      <td>NAME=VALUE</td>
      <td>键值对，可以设置要保存的 Key/Value，注意这里的 NAME 不能和其他属性项的名字一样</td>
   </tr>
   <tr>
      <td>Expires</td>
      <td>过期时间，在设置的某个时间点后该 Cookie 就会失效</td>
   </tr>
   <tr>
      <td>Domain</td>
      <td>生成该 Cookie 的域名，如 domain="www.baidu.com"</td>
   </tr>
   <tr>
      <td>Path</td>
      <td>该 Cookie 是在当前的哪个路径下生成的，如 path=/wp-admin/</td>
   </tr>
   <tr>
      <td>Secure</td>
      <td>如果设置了这个属性，那么只会在 SSH 连接时才会回传该 Cookie</td>
   </tr>
</table>

## 设置cookie

```javascript
function setCookie(json,time){
    var date = new Date(new Date().getTime()+time*24*60*60*1000).toUTCString();
    for(var key in json){
        document.cookie = key+"="+json[key]+";expires="+date
    }
}
setCookie({
    name:'wanglina',
    sex:'female'
},7);

```

## 获取cookie
```javascript
function getCookie(attr){
    return document.cookie.match(new RegExp("(^|\\s)"+attr+"=([^;]+)(;|$)"))
}
```


## 清除cookie
```javascript
function removeCookie(attr){
    var json = {};
    json[attr] = "";
    setCookie(json,-1)
}
```

## javascript如何判断浏览器是否禁用cookie
语法：`navigator.cookieEnabled`；
如果浏览器启用了`cookie`，该属性值为`true`。如果禁用了`cookie`，则值为`false`。

`navigator`：
 `JavaScript`中的一个独立的对象，他用于提供用户所使用的浏览器以及操作系统等信息，以`navigator`对象属性的形式来提供。


## 防止javascript脚本读取cookie信息

1.设置HttpOnly
如果您在cookie中设置了HttpOnly属性，那么通过js脚本将无法读取到cookie信息，这样能有效的防止XSS攻击。

2.HttpOnly的设置样例

```javascript
response.setHeader("Set-Cookie", "cookiename=value;Path=/;Domain=domainvalue;Max-Age=seconds;HTTPOnly");
```
```bash
//设置http的cookie
response.setHeader("Set-Cookie", "uid=112; Path=/; HttpOnly");
```
```bash
//设置https的cookie
response.setHeader("Set-Cookie", "uid=112; Path=/; Secure; HttpOnly");
```
 

 ## WebStorage
 `WebStorage`的目的是克服由`cookie`所带来的一些限制，当数据需要被严格控制在客户端时，不需要持续的将数据发回服务器。

#### WebStorage两个主要目标：
1.提供一种在`cookie`之外存储会话数据的路径
2.提供一种存储大量可以跨会话存在的数据的机制

`HTML5`的`WebStorage`提供了两种`API`：`localStorage`（本地存储）和`sessionStorage`（会话存储）

### localStorage和sessionStorage的区别

##### 1.生命周期

`localStorage`的生命周期是永久的，关闭页面或浏览器之后`localStorage`中的数据也不会消失。`localStorage`除非主动删除数据，否则数据永远不会消失。
`sessionStorage`的生命周期是仅在当前会话下有效。`sessionStorage`引入了一个“浏览器窗口”的概念，`sessionStorage`是在同源的窗口中始终存在的数据。只要这个浏览器窗口没有关闭，即使刷新页面或者进入同源另一个页面，数据依然存在。但是`sessionStorage`在关闭了浏览器窗口后就会被销毁。同时独立的打开同一个窗口同一个页面，`sessionStorage`也是不一样的。

##### 2.存储大小
`localStorage`和`sessionStorage`的存储数据大小一般都是：5MB

##### 3.存储位置
`localStorage`和`sessionStorage`都保存在客户端，不与服务器进行交互通信

##### 4.存储内容类型
`localStorage`和`sessionStorage`只能存储字符串类型，对于复杂的对象可以使用`ECMAScript`提供的`JSON`对象的`stringify`和`parse`来处理

##### 5.获取方式
```bash
localStorage：window.localStorage
sessionStorage：window.sessionStorage
```
##### 6.应用场景
`localStorage`：常用于长期登录（+判断用户是否已登录），适合长期保存在本地的数据
`sessionStorage`：敏感账号一次性登录

## WebStorage的优点
- 存储空间更大：`cookie`为4KB，而`WebStorage`是5MB
- 节省网络流量：`WebStorage`不会传送到服务器，存储在本地的数据可以直接获取，也不会像`cookie`一样每次请求都会传送到服务器，所以减少了客户端和服务端的交互，节省了网络流量
- 对于那种只需要在用户浏览一组页面期间保存而关闭浏览器后就可以丢弃的数据，`sessionStorage`会非常方便
- 快速显示：有的数据存储在`WebStorage`上再加上浏览器本身的缓存。获取数据时可以从本地获取会比从服务器端获取快得多，所以速度更快
- 安全性：`WebStorage`不会随着`HTTP header`发送到服务器端，所以安全性相对于`cookie`来说会比较高一些，不会担心截获，但是仍然存在伪造问题
- `WebStorage`提供了一些方法，数据操作比`cookie`方便
```bash
setItem(key, value) —— 保存数据，以键值对的方式存储信息
getItem(key) —— 获取数据，将键值传入，即可获取到对应的value值
removeItem(key) —— 删除单个数据，根据键值移除对应的信息
clear() —— 删除所有的数据
key(index) —— 获取某个索引的key
```

