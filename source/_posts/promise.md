---
title: promise相关场景题
date: 2020-04-12 16:48:28
categories: 前端
tags: 
    - 异步
---



序
这篇文章主要总结回顾promise的相关场景题，关于promise的基础知识本篇文章不作介绍，可自行了解

### 接口超时取消

```javascript
function abortReject(promise, url) {
    let abort = null
    let abort_promise = new Promise((resolve, reject) => {
        abort = () => {
            reject('abort')
            console.log('abort requset', 'url:', url)
        }
    })
    promise = Promise.race([promise, abort_promise])
    promise.abort = abort
    return promise
}
```


### 已有请求函数query，其功能为异步请求数据返回promise对象，如query(params).then(…).catch(…)。实现一个fetchData,返回promise对象，要求加入失败重试功能，该函数内部依然使用query实现，在query失败一次后间隔一秒钟再重试一次，直到重试到第五次、如果全都失败了，fetchData所返回的promise为reject，只要有任意一次成功，则停止重试，知道resolve结果。


```javascript
function query(){
	let p = new Promise((resolve,reject)=>{
		var num = Math.random()*20;
		console.log('随机数生成的值：',num)
		if(num<=1){
			 console.log('符合条件，值为'+num)
			resolve(num)
		}else{
			reject('数字大于10了执行失败');
		}
	})
	return p
}

//也返回一个promise，但是有失败重试功能
function fetData(query, times, delay) {
    return new Promise(function(resolve, reject) {
       function attempt () {
	        query()
	        .then(resolve)
	        .catch(function(error) {
	        console.log(`还有 ${times} 次尝试`)
	          if (0 == times) {
	            reject(error)
	          } else {
	            times--
	            setTimeout(attempt(), delay)
	          }
	        })
      }
       attempt()
    })
 }

//通过调用myGetData来看下执行效果，是否达到五次失败内可以重试：
 this.fetData(query,5,1000)
```
