---
layout: post
title: 将nodejs中的回调地狱改为async/await方式
category: 技术
tags: Jekyll
description: 将nodejs中的回调地狱改为async/await方式
---

### nodejs异步代码历史
1. callback
2. Promise
3. yeild
4. async/await

javascript最直观的异步代码写法为callback，如setTimeout函数
`setTimeout(function(){},1000)`
或者request库的http请求：
`request(option,function(){})`
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

因此，为了解决回调代码写法导致的可读性差问题，社区进行了持续的探索

首先流行起来的是Promise库

Promise可以理解为一个状态机，只会处于三种状态：Pending、Resolve、Reject
- pending表示回调正在执行，尚未获得执行结果
- resolve表示回调执行完毕，且执行成功
- reject表示回调执行完毕，但执行失败

Pending状态一旦转换到Resovle或Reject状态，则无法回退或更改
Promise永远返回一个Promise对象，而不会返回执行结果的具体值

将回调函数写法改为Promise写法比较简单，只需要在异步函数外层定义一个Promise，将整个异步函数包起来，在异步函数的回调函数中，判断执行成功，则执行resolve，判断异步执行失败，则执行reject
如将setTimeout异步函数改写为promise写法，得到如下：

```javascript
function promise_setTimeout(){
    return new Promise(function(resolve,reject){
        setTimeout(function(result){
            somecode...
            if(result===“success”){//回调函数执行成功
                resolve(result)
            }
            else{//回调函数执行失败
                reject(result)
            }
        },1000)
    })
}
```

以上改写后的函数 promise_setTimeout 不会立即执行，这与setTimeout函数执行不一致，因此将此函数进一步改写为立即执行函数
```javascript
(function promise_setTimeout(){
    somecode...
    if...
    else...
})()
```

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