---
title: Promise踩坑记
date: 2020-07-23 14:46:25
categories: Promise
tags: 
    - 异步
---



### 序
异步回调的一个很大的问题在于callback hell也就是“回调地狱”。多层嵌套回调函数，严重影响代码规范，所以Promise的出现就是为了解决callback hell，在前端应用中得到广泛的应用，所以想要用好它还得遵循它的规范。下面结合最近做的需求做了一些总结，对其加深印象：


### 分析问题：

Promise链式引发的问题，现在需要返回一个Promise

目标：满足A条件则需要reject()然后触发外层的catch()，形成一个链式，这样就不能走到外部的Promise.then(()=>{})，才不可能走到B流程，反之，resolve()，要走到也就是B流程，不符逻辑

### 异常捕获
Promise有两种异常捕获方式，一个是then中的reject，另一个是catch()方法

Promise.prototype.catch方法返回也是一个Promise，使用方式同then方法，用于处理异常捕获情况。该方法捕获属于就近捕获原则，距离错误最近的catch将会捕获错误，后续不再捕获。

```javascript
let p = new Promise((resolve,reject)=>{
  reject('error')
})
p.then(()=>{
  
},(err) =>{
  console.log(err)     //error
})



let p1 = new Promise((resolve,reject)=>{
  resolve("success")
})
p1.then(value=>{
  throw new Error('fail!')
}).catch(e=>{
  //如果返回reject时会调用这个回调函数
  console.log(e)
})
```
区别：
- `catch` 捕获的是上一个`then`方法出现的异常
- `then`方法第二个参数捕获的是当前`promise`出现的异常

如果在`Promise`链条中发生异常，这个异常会被一直往后传递，直到被捕获，所以`catch`方法是整个链条注册的捕获


### 解决问题：

返回的内部`Promise`不要`catch()`，否则内部`Promise`出现`error`是走不到外层`catch()`的，就会一直走到B流程，不符合业务逻辑场景



### 注意事项：

`async await` 一定要加`try catch`，一定要加`try catch`，一定要加`try catch`，重要的事情说三遍～  如果有需要可以根据个人需求加上`finally`


### example：

```javascript
handlePreventStatus = async () => {
 this.showLoading()
 try {
  //返回一个promise
  let res = await this.getPreventStatus();
  //your coding
 }
 catch (error) {
 // 抛出异常
 console.log(error);
 }
 finally {
  // 无论try还是catch都会走到这里，个人根据自己的需求视情况coding
  this.hideLoading()
 }
}
```


### 规范：

- `Promise`要符合`Promise/A+`规范，下面将揭开它的神秘面纱～

- `Async Await` 以同步的方式处理异步，使用它的时候一定要加`try catch` ，其实它就是个`Generator+ Promise`，下面将揭开它的神秘面纱～ 

### Promise源码：
```javascript
//Promise构造函数源码
const FULL_FILLED = 'fullFilled';// 成功
const REJECTED = 'rejected'; //失败
const PENDING = 'pending'; //初始状态
let Promise = function(fn){
    this.status = PENDING;
    this.successCallbacks = [];
    this.failedCallbacks = [];
    let resolve = (v) => {
        //resolve(value)
        this.value = v;
        this.status = FULL_FILLED;
        this.successCallbacks.forEach(fn=>fn(this.value))
    }
    let reject = (v) => {
        this.value = v;
        this.status = REJECTED;
        this.failedCallbacks.forEach(fun => fn(this.value))
    }
    try{
        fn(resolve,reject)
    }catch(e){
        reject(e)    //new promise.then() 执行失败
    }
}

//递归promise
function excutePromise(promiseOrVal,resolve,reject) {
    if(promiseOrVal instanceof Promise){
        promiseOrVal.then((v)=>{
            excutePromise(v,resolve,reject)
        })
    }else{
        //value基本数据类型
        resolve(promiseOrVal);
    }
}

//then一定要返回Promise供外部.then
Promise.prototype.then = function(onFullFilledFn,onRejectedFn){
    let chainPromise;
    //如果当前,3s以内,pending
    if(this.status === PENDING){
            chainPromise = new Promise((resolve,reject)=>{
                //保存函数
                this.successCallbacks.push(()=>{
                    //resolve后才会被发布,所执行
                    try{
                        setTimeout(()=>{
                            let value = onFullFilledFn(this.value)   //value有可能是promise也有可能是一般值
                            excutePromise(value,resolve,reject)
                        })
                    }catch(e){
                        reject(e)
                    }
                })             
                this.failedCallbacks.push(()=>{
                    try{
                        setTimeout(()=>{
                        	let value = onRejectedFn(this.value)   //value有可能是promise也有可能是一般值
                            excutePromise(value,resolve,reject)
                        })
                    }catch(e){
                        reject(e)
                    }
                })

             }) 
    }
    if(this.status === FULL_FILLED){
        chainPromise = new Promise((resolve,reject)=>{
            try{
                setTimeout(()=>{
                    let value = onFullFilledFn(this.value)
                    excutePromise(value,resolve,reject)
                })
            }catch(e){
                reject(e)
            }
        })
    }
    if(this.status === REJECTED){
        chainPromise = new Promise((resolve,reject)=>{
            try{
                setTimeout(()=>{
                    let value = onRejectedFn(this.value)
                    excutePromise(value,resolve,reject)
                })
            }catch(e){
                reject(e)
            }
        })
    }
    return chainPromise
}

//模拟3组异步,返回的是promise实例
new Promise(function(resolve,reject){
        console.log('promise执行了')    //promise构造器中的函数参数,一经实例化,立即执行
        setTimeout(()=>{
            console.log('第一件事....')
            resolve('abcd');
        },3000)
}).then((data)=>{
    console.log('刚才成功了resolve')
    console.log('接收到的数据为:',data)
    return new Promise((resolve,reject)=>{
        setTimeout(()=>{
            console.log('第2件事。。。。')
            resolve('xxx')     //3s后调用resolve函数
        },3000);
    }); 
}).then((d)=>{
    console.log('结束了',d)
})

```





### await源码：

```javascript

async function fn(){
    let v1 = await Promise.resolve(100);
    console.log(v1);
    let v2 = await Promise.resolve(200);
    console.log(v2)
}


//await实现原理是从Generator来的
function yieldPromise(genrator){
    let interator = genrator();
    interatorNext.call(interator)
}

function interatorNext(value){
    result = interator.next(value)
    if(result.done) return
    let val = result.value;
    Promise.resolve(val).then(val=>{
        interatorNext.call(interator,val)
    })
}

yieldPromise(function* (){
    let v1 = yield Promise.resolve(100)
    console.log(v1)
})

```

### 总结
```javascript
Promise.prototype.then()
Promise.prototype.catch()
//ES2018 引入
Promise.prototype.finally()

  
Promise.all() 
Promise.race() 
Promise.resolve() 
Promise.reject()

//ES2020 新提案
Promise.allSettled()    
Promise.any()
```



