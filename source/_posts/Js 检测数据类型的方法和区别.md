---
title: Js 检测数据类型的方法和区别
top: false
cover: false
toc: true
mathjax: true
comments: true
date: 2023-09-04 18:44:35
password:
summary:
tags:
- 前端
- Js
categories:
- [前端, Js]
id: 2
---
JS的数据类型判断有 typeof()、instanceof()、constructor、Object.prototype.toString.call()等方法，但是他们也有区别。
<!--more-->
* 参考CSDN [zhongjunyao](https://blog.csdn.net/zjy_android_blog/article/details/81023177)
# typeof
```js codes
// js
console.log(typeof 2);               // number
console.log(typeof true);            // boolean
console.log(typeof 'str');           // string
console.log(typeof []);              // object     数组的数据类型在 typeof 中被解释为 object
console.log(typeof function(){});    // function
console.log(typeof {});              // object
console.log(typeof undefined);       // undefined
console.log(typeof null);            // object     null 的数据类型被 typeof 解释为 object
```
:::danger
* ++数字Number，布尔值Boolean，字符串String，函数Function，对象Object，Undefined++{.primary}这一些数据类型在typeof 下都被精准的解释，只有数组和null的数据类型不够精准。其实typeof对于数组 和 null 的类型判断是正确的，只不过不够精准而已。
:::

# instanceof
```js codes
// js
console.log(2 instanceof Number);                    // false
console.log(true instanceof Boolean);                // false 
console.log('str' instanceof String);                // false  
console.log([] instanceof Array);                    // true
console.log(function(){} instanceof Function);       // true
console.log({} instanceof Object);                   // true    
console.log(undefined instanceof Undefined);         // Uncaught ReferenceError: Undefined is not defined
console.log(null instanceof Null);                   // Uncaught ReferenceError: Null is not defined
```
* 直接的字面量值判断数据类型，只有++引用数据类型（Array，Function，Object）++{.primary}被精准判断，++其他（数值Number，布尔值Boolean，字符串String）++{.primary}字面值不能被instanceof精准判断。
*  instanceof 在MDN中的解释：++instanceof 运算符用来测试一个对象在其原型链中是否存在一个构造函数的 prototype 属性。++{.dot}其意思就是判断对象是否是某一数据类型（如Array）的实例，请重点关注一下是判断一个[对象]{.red}是否是数据类型的实例。
* 上面案例可以更改如下：
```js codes
//js
console.log(new Number(2) instanceof Number);               //true
console.log(new Boolean(true) instanceof Boolean);          //true
console.log(new String('str') instanceof String);           //true
```
* 对于null和undefined
:::info
* 浏览器认为null，undefined不是构造器。但是在 typeof 中你可能已经发现了，typeof null的结果是object，typeof undefined的结果是undefined ，这是怎么回事呢?
* 尤其是null，其实这是js发展过程中设计者的重大失误，早期准备更改null的类型为null，由于当时已经有大量网站使用了null，如果更改，将导致很多网站的逻辑出现漏洞问题，就没有更改过来，于是一直遗留到现在。作为学习者，我们只需要记住就好。
:::
# constructor
```js codes
console.log((2).constructor === Number);
console.log((true).constructor === Boolean);
console.log(('str').constructor === String);
console.log(([]).constructor === Array);
console.log((function() {}).constructor === Function);
console.log(({}).constructor === Object);
```
* 用costructor来判断类型看起来是完美的，然而，如果我创建一个对象，更改它的原型，这种方式也变得不可靠了。
```js codes
//js
function Fn(){};
 
Fn.prototype=new Array();
 
var f=new Fn();
 
console.log(f.constructor===Fn);    // false
console.log(f.constructor===Array); // true 
```
# Object.prototype.toString.call
```js codes
//js
var a = Object.prototype.toString;
 
console.log(a.call(2));               // [object Number]
console.log(a.call(true));            // [object Boolean]
console.log(a.call('str'));           // [object String]
console.log(a.call([]));              // [object Array]
console.log(a.call(function(){}));    // [object Function]
console.log(a.call({}));              // [object Object]
console.log(a.call(undefined));       // [object Undefined]
console.log(a.call(null));            // [object Null]
```
* 使用 Object 对象的原型方法 toString ，使用 call 进行狸猫换太子，借用Object的 toString  方法，结果精准的显示我们需要的数据类型。

[:heart:筱何仔]{.label .danger}