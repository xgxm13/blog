---
title: 请求动画帧 requestAnimationFrame
top: false
cover: false
toc: true
mathjax: true
comments: true
date: 2021-11-16 17:13:34
password:
summary:

categories:
- [前端, Js]
id: 1
---
项目中遇到用 [setInterval]{.blue} 写的滚动的看板，在低配置的电视上比较卡顿， 同事推荐了 [请求动画帧]{.blue} [requestAnimationFrame](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame)
<!-- More -->
## 介绍
[window.requestAnimationFrame()]{.red} 告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行
* 注意：若你想在浏览器下次重绘之前继续更新下一帧动画，那么回调函数自身必须再次调用[window.requestAnimationFrame()]{.red}

## 语法
[window.requestAnimationFrame(callback);]{.red}

## 参数
[callback]{.blue}
下一次重绘之前更新动画帧所调用的函数(即上面所说的回调函数)。该回调函数会被传入 [DOMHighResTimeStamp]{.blue} 参数，该参数与[performance.now()](https://developer.mozilla.org/zh-CN/docs/Web/API/Performance/now)的返回值相同，它表示[requestAnimationFrame()]{.blue} 开始去执行回调函数的时刻。
* [requestAnimationFrame()]{.blue} 方法返回一个精确到毫秒的 [DOMHighResTimeStamp]{.blue} 。

## 返回值
一个 long 整数，请求 ID ，是回调列表中唯一的标识。是个非零值，没别的意义。你可以传这个值给 [window.cancelAnimationFrame()]{.blue} 以取消回调函数。

## 滚动例子
```js 代码
// html
<div id='scroll'>
  // ... 循环的代码
</div>
// css
#scroll {
  height: 200px
  overflow: auto 
}
// js
const scroll = document.getElementById('scroll');
function scroll() {
  scroll.scrollTop = wrap.scrollTop + 1;
  if (scroll.scrollTop >= scroll.scrollHeight - scroll.offsetHeight) {
    scroll.scrollTop = 0;
  }
  requestAnimationFrame(scroll);
}
scroll();

```
## 自定义时间间隔 
* 如果我想自己设定一个时间，不让跟着屏幕刷新率来，如何操作？
* 利用固定的时间差 参考 [简书：我是一个前端的博客](https://www.jianshu.com/p/fa5512dfb4f5)
```js 代码
// js
const scroll = document.getElementById('scroll');
//当前执行时间
let nowTime = 0;
//记录每次动画执行结束的时间
let lastTime = Date.now();
//我们自己定义的动画时间差值
let diffTime = 50;

function scroll() {
  //记录当前时间
  nowTime = Date.now()
  // 当前时间-上次执行时间如果大于diffTime，那么执行动画，并更新上次执行时间
  if(nowTime-lastTime > diffTime){
      lastTime = nowTime
      wrap.scrollTop = wrap.scrollTop + 1;
      if (wrap.scrollTop >= wrap.scrollHeight - wrap.offsetHeight) {
        wrap.scrollTop = 0;
      }
  }
  scroll.scrollTop = wrap.scrollTop + 1;
  if (scroll.scrollTop >= scroll.scrollHeight - scroll.offsetHeight) {
    scroll.scrollTop = 0;
  }
  requestAnimationFrame(scroll);
}
scroll();
```
## 总结
* 与 [setTimeout]{.blue} 和 [setInterval]{.blue} 不同，[requestAnimationFrame]{.blue} 不需要设置时间间隔，是根据浏览器屏幕刷新次数来的，一般来说，这个频率为每秒60帧，如果要设置间隔，参考上文方法。体验来讲，确实比较流畅，特别是低配置车间电视上。
* 在大多数浏览器里，当 [requestAnimationFrame()]{.blue} 运行在后台标签页或者隐藏的 [<iframe>]{.blue} 里时，[requestAnimationFrame()]{.blue} 会被暂停调用以提升性能和电池寿命。
* 存在兼容问题，其他浏览器需要加前缀。

[:heart:筱何仔]{.label .danger}