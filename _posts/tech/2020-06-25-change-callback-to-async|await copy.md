---
layout: post
title: 将nodejs中的回调地狱改为async/await方式
category: 技术
tags: Jekyll
description: 将nodejs中的回调地狱改为async/await方式
---

### nodejs异步代码历史
1. callback: (hell)
2. Promise: ES2015标准中加入，此前有第三方库如bluebird、co实现
3. Generator: ES2015标准中加入，基于Promise，提供了yeild语法糖
4. async/await: ES2017新特性，基于Promise；最早有第三方async模块，只是名称类似，详见附录

***
## 1. callback hell
javascript最直观的异步代码写法为callback，如setTimeout函数
```javascript
    setTimeout(function(){
        if(true){//回调函数执行成功
            console.log("success")
        }
        else{//回调函数执行失败
            console.log("error")
        }
    },1000)
```
或者request库的http请求：
```javascript
request(option,function(){})
```
以上两个函数参数中的function即为回调函数

回调函数写法在功能实现上没什么问题，只是面向过程的编程方式中，以下代码组织方式更符合阅读习惯:

```javascript
setTimeOut()
functionA()
request()
functionB()
```

另外回调函数在后续代码依赖前面代码的执行结果时，多个阶段的代码必须使用嵌套实现，从而导致"回调地狱"
```javascript
setTimeout(function(){
    request(option,function(){
        setInterval(function(){

        },1000)
    })
},1000)
```

由于回调函数执行结果的未知性，导致只能掉入回调地狱，代码的可读性变差，为了解决这个问题，社区进行了持续的探索

***

## 2. Promise

Promise可以理解为一个状态机，只会处于三种状态中的某一种：Pending、Resolve、Reject
- pending表示回调正在执行，尚未获得执行结果
- resolved表示回调执行完毕，且执行成功
- rejected表示回调执行完毕，但执行失败

Pending状态一旦转换到Resolve或Reject状态，则无法回退或更改。resolved状态的另外一个说法是fulfilled状态

将回调函数写法改为Promise写法比较简单，如将setTimeout异步函数改写为promise写法，得到如下：

```javascript
var promise_setTimeout= new Promise(function(resolve,reject){
    console.log("flag 1")
    setTimeout(function(){
        if(true){//回调函数执行成功
            console.log("flag 2")
            resolve("success")
        }
        else{//回调函数执行失败
            reject("error")
        }
    },1000)
})
```

Promise永远返回一个Promise对象，而不会返回执行结果的具体值，代码中的resolve、reject分别是Promise.resolve和Promise.reject方法，作用就是将其非Promise的参数"success"和"error"转化为Promise对象（Promise.resolve和Promise.reject作用到参数时，要求参数是拥有then方法的thenable对象，否则转换后的对象自动处于resolved或rejected状态，这也是为何此处可以用字符串参数来实现异步函数执行成功或执行失败状态的模拟）

由上述例子我们看到，只需要在异步函数外层定义一个Promise对象，将整个异步函数包起来，在异步函数的回调函数中，判断执行成功，则调用resolve方法返回执行成功的Promise对象；判断异步执行失败，则调用reject方法返回执行失败的Promise对象即可

以上改写后的Promise对象promise_setTimeout，在创建后就会立即执行，因此flag1先被输出，等待1秒钟后输出flag2和success，表示回调函数的执行结果为成功

然而目前我们并没有达到预想目标，改写后的回调函数要具备优秀的顺序可读性，关键在于获取到Promise对象后，使用then获取成功执行的结果，使用catch获取执行失败的结果

```javascript
var promise_setTimeout= new Promise(function(resolve,reject){
    console.log("flag 1")
    setTimeout(function(){
        if(true){//回调函数执行成功
            console.log("flag 2")
            resolve("success")
        }
        else{//回调函数执行失败
            reject("error")
        }
    },1000)
})
//下面一行代码的写法为：只处理回调函数成功执行后的结果(不进行异常捕获是很危险的)
promise_setTimeout.then(function(result){})
//下面一行代码的写法为：只捕获执行失败时reject带回的异常
promise_setTimeout.catch(function(error){})
//下面一行代码的写法为：回调函数执行成功时获取执行结果，执行失败时捕获异常
promise_setTimeout.then(function(result){}).catch(function(error){})

```
有关上述代码的几点补充说明：
1. 此setTimeout的回调函数中，由于永远进入if(true)分支，因此始终会返回resolved状态的Promise对象
2. resolve和reject都只是参数变量，改为okokok或者nonono代表Promise.resolve和Promise.reject方法也没问题
3. 何时为回调执行成功、何时为回调执行失败，全凭何时调用resolve和reject
4. 回调执行时抛出的异常，或者返回的reject，若没有catch捕获，则会报错"UnhandledPromiseRejectionWarning: Unhandled promise rejection."

***

## 3. Generator



***

## 4. async/await

asyncFunction(somecode,function(params){
    doSomeThingWithParams;
})

改写为async/await:
function fn(){
    return new Promise(
        resolve=>{
            funName(socode,callback(params)=>{
                doSomeThingWithParams;
                return resovle(result);
            })
        })
}
mainFn = async function(){
    var r = await fn();
}