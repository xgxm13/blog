---
title: JS宏任务和微任务
top: false
cover: false
toc: true
mathjax: true
comments: true
date: 2023-07-12 17:46:41
password:
summary:
tags:
- 前端
categories:
- [前端]
id: 1
---
JS宏任务和微任务
* 前排感谢 CSDN的 [ 奔波儿灞... ](https://blog.csdn.net/mrszhang111/article/details/126861669)
<!--More-->
# 什么是微任务和宏任务
* 首先，我们要先了解下 Js 。Js 是一种单线程语言，简单的说就是：只有一条通道，那么在任务多的情况下，就会出现拥挤的情况，这种情况下就产生了 ‘多线程’ ，但是这种“多线程”是通过单线程模仿的，也就是假的。那么就产生了同步任务和异步任务。
# JS为什么要区分微任务和宏任务
* （1）js是单线程的，但是分同步异步
* （2）微任务和宏任务皆为异步任务，它们都属于一个队列
* （3）宏任务一般是：script、setTimeout、setInterval、postMessage、MessageChannel、setImmediate(Node.js 环境)
* （4）微任务：Promise.then、Object.observe、MutationObserver、process.nextTick(Node.js 环境)
* （5）先执行同步再执行异步，异步遇到微任务，先执行微任务，执行完后如果没有微任务，就执行下一个宏任务，如果有微任务，就按顺序一个一个执行微任务
# 微任务和宏任务有哪些
* （1）宏任务一般是：script、setTimeout、setInterval、postMessage、MessageChannel、setImmediate(Node.js 环境)

* （2）微任务：Promise.then、Object.observe、MutationObserver、process.nextTick(Node.js 环境)
# 微任务和宏任务是怎么执行的？
* 执行顺序：先执行同步代码，遇到异步宏任务则将异步宏任务放入宏任务队列中，遇到异步微任务则将异步微任务放入微任务队列中，当所有同步代码执行完毕后，再将异步微任务从队列中调入主线程执行，微任务执行完毕后再将异步宏任务从队列中调入主线程执行，一直循环直至所有任务执行完毕。

这里容易产生一个错误的认识：就是微任务先于宏任务执行。实际上是先执行同步任务然后在执行异步任务，异步任务是分宏任务和微任务两种的。

# 案例
## 例1
```js code
setTimeout(function(){
    console.log(1);
});
new Promise(function(resolve){		    
    console.log(2);
    resolve();
}).then(function(){		    
    console.log(3);
}).then(function(){
    console.log(4)
}); 		
console.log(5);
```
* 输出结果 (鼠标滑过显示内容)
!!2 5 3 4 1!! 
* 分析 
:::info
    1.遇到setTimout，异步宏任务，放入宏任务队列中
    2.遇到new Promise，new Promise在实例化的过程中所执行的代码都是同步进行的，所以输出2
    3.Promise.then，异步微任务，将其放入微任务队列中
    4.遇到同步任务console.log(5);输出5；主线程中同步任务执行完
    5.从微任务队列中取出任务到主线程中，输出3、 4，微任务队列为空
    6.从宏任务队列中取出任务到主线程中，输出1，宏任务队列为空
:::



## 例2
```js code
setTimeout(()=>{
    new Promise(resolve =>{
        resolve();
    }).then(()=>{
        console.log('test');
    });
    console.log(4);
});
new Promise(resolve => {
    resolve();
    console.log(1)
}).then( () => {
    console.log(3);
    Promise.resolve().then(() => {
        console.log('before timeout');
    }).then(() => {
        Promise.resolve().then(() => {
          console.log('also before timeout')
        })
    })
})
console.log(2);
//1，2，3，before timeout，also before timeout，4，test;
```
* 输出结果 (鼠标滑过显示内容)
!!1，2，3，before timeout，also before timeout，4，test!! 
* 分析 
:::info
    1.遇到setTimeout，异步宏任务，将() => {console.log(4)}放入宏任务队列中；
    2.遇到new Promise，new Promise在实例化的过程中所执行的代码都是同步进行的，所以输出1；
    3.而Promise.then，异步微任务，将其放入微任务队列中
    4.遇到同步任务console.log(2)，输出2；主线程中同步任务执行完
    5.从微任务队列中取出任务到主线程中，输出3，此微任务中又有微任务，Promise.resolve().then(微任务a).then(微任务b)，将其依次放入微任务队列中；
    6.从微任务队列中取出任务a到主线程中，输出 before timeout；
    7.从微任务队列中取出任务b到主线程中，任务b又注册了一个微任务c，放入微任务队列中；
    8.从微任务队列中取出任务c到主线程中，输出 also before timeout；微任务队列为空
    9.从宏任务队列中取出任务到主线程，此任务中注册了一个微任务d，将其放入微任务队列中，接下来遇到输出4，宏任务队列为空
    10.从微任务队列中取出任务d到主线程 ，输出test，微任务队列为空
:::
## 例3
```js code
console.log(1)
setTimeout(function() {
    console.log(2)
}, 0)
const p = new Promise((resolve, reject) => {
    resolve(4)
})
p.then(data => {
    console.log(data)
})
console.log(3)
```
* 输出结果 (鼠标滑过显示内容)
!!1，3，4，2!! 
* 分析
:::info
    1.遇到同步任务console.log(1);输出1；
    2.遇到setTimeout 异步宏任务，放入宏任务队列中；
    3.遇到 Promise，new Promise在实例化的过程中所执行的代码都是同步进行的，但由于new Promise没有输出事件，所以接着执行遇到.then；
    4.执行.then，异步微任务,被分发到微任务Event Queue中；
    5.遇到同步任务console.log(3);输出3；
    6.主线程中同步任务执行完，从微任务队列中取出任务到主线程中，p.then 输出4,微任务执行完毕，任务队列为空；
    7.开始执行宏任务setTimeout 输出2,宏任务队列为空；

:::
## 例4
```js code
console.log(1)
setTimeout(function() {
    console.log(2)
    new Promise(function(resolve) {
        console.log(3)
        resolve()
    }).then(function() {
        console.log(4)
    })
})
 
new Promise(function(resolve) {
    console.log(5)
    resolve()
}).then(function() {
    console.log(6)
})
 
setTimeout(function() {
    console.log(7)
    new Promise(function(resolve) {
        console.log(8)
        resolve()
    }).then(function() {
        console.log(9)
    })
})
console.log(10)
```
* 输出结果 (鼠标滑过显示内容)
!!1，5，10，6，2，3，4，7，8，9!! 
* 分析
:::info
    1.遇到同步任务console.log(1);输出1；
    2.遇到setTimeout 异步宏任务，放入宏任务队列中；
    3.遇到 Promise，new Promise在实例化的过程中所执行的代码都是同步进行的，所以输出5，所以接着执行遇到.then；
    4.执行.then，异步微任务,被分发到微任务Event Queue中；
    5.遇到setTimeout，异步宏任务;放入宏任务队列中；
    6.遇到同步任务console.log(10);输出10,主线程中同步任务全部执行完;
    7.从微任务队列中取出任务到主线程中，输出6；
    8.在从宏任务队列中取出任务到主线程中，执行第一个setTimeout，输出2，3，4（在宏任务中执行同步,同步，异步微任务）;
    9.在执行第二个setTimeout，输出7，8，9（和8同理）；
:::
## 例5
```js code
new Promise((resolve, reject) => {
    resolve(1)
    new Promise((resolve, reject) => {
        resolve(2)
    }).then(data => {
        console.log(data)
    })
}).then(data => {
    console.log(data)
})
console.log(3)
//
```
* 输出结果 (鼠标滑过显示内容)
!!3，2，1!! 
* 分析
:::info
    1.遇到Promise,new Promise在实例化的过程中所执行的代码都是同步进行的,但由于new Promise没有输出事件，所以接着往下执行遇到new Promise没有输出事件再接着往下执行遇到.then,异步微任务,被分发到微任务Event Queue中，再接着 .then 放入微任务
    2.遇到同步任务console.log(3) 输出3，主线程中同步任务执行完；
    3.从微任务队列中取出任务到主线程中，输出2，1，微任务执行完毕，任务队列为空。

:::
# 总结
:::success
微任务和宏任务的执行顺序是先执行同步任务，先执行同步后异步，异步分为宏任务和微任务两种，异步遇到微任务先执行微任务，执行完后如果没有微任务，就执行下一个宏任务。
:::
[:heart:筱何仔]{.label .danger}