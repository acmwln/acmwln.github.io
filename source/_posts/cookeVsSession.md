---
title: cookie和session
date: 2020-03-02 10:47:14
categories: 网络
tags:
    - 网络
---

## 序
cookie VS session

## 介绍
session和cookie都是会话跟踪技术，cookie通过在客户端记录信息确定用户身份；session通过在服务器端记录信息确定用户身份，但是session的实现依赖于cookie。sessionId（session的唯一标识需要存放在客户端）

## cookie
- cookie是服务器响应报文的信息，存储在客户端中。
- 不设置过期时间的cookie，存储在内存中，生命周期在会话期间，关闭浏览器窗口，cookie就消失。称为会话cookie
- 设置了过期时间的cookie，会在硬盘中，关闭了浏览器再次打开还是会读取cookie，只要不超过时间

## session

session机制采用一种在服务器保持活跃的状态的解决方案。session在客户端也需要保存一个标识，所以session可能需要借助cookie来达到保存标识的目的。
session是针对每一个用户的，变量的值保存在服务器，用一个sessionID来区分哪个用户session变量。

## 相同点
session和cookie都是由服务器生成的
session和cookie都是键值对，即session和cookie就是用来保存特定的值的一种技术
## 区别
1、cookie数据存放在客户端的浏览器上，session数据放在服务器上。
2、cookie不是很安全，别人可以分析存放在本地的cookie并进行cookie欺骗，考虑到安全应当使用session。
3、session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能，考虑到减轻服务器性能方面，应当使用cookie。
4、单个cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个cookie。
5、可以考虑将登陆信息等重要信息存放为session，其他信息如果需要保留，可以放在cookie中。

## 场景
1.登录网站，今天输入用户名密码登录了，第二天再打开很多情况下就直接打开了。这个时候用到的一个机制就是cookie。
2.session一个场景是购物车，添加了商品之后客户端处可以知道添加了哪些商品，而服务器端如何判别呢，所以也需要存储一些信息就用到了session
3.购物车最好使用cookie，但是cookie是可以在客户端禁用的，这时候我们要使用cookie+数据库的方式实现，当从cookie中不能取出数据时，就从数据库获取。


##  cookie机制(一个cookie的设置以及发送过程分为以下四步)：
- 客户端发送一个http请求到服务器端
- 服务器端发送一个http响应到客户端，其中包含Set-Cookie头部
- 客户端发送一个http请求到服务器端，其中包含Cookie头部
- 服务器端发送一个http响应到客户端

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
