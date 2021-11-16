---
title: 跳出forEach循环
top: false
cover: false
toc: true
mathjax: true
comments: true
date: 2021-11-15 16:50:01
password:
summary:
- 前端
- Js
categories:
- [前端, Js]
id: 1
---
有些业务场景需要用的 [forEach]{.red} 比如需要遍历复杂数据，只要出现一个满足条件的数据就终止掉整个循环。
<!--more-->
##  跳出本次循环
```js 代码
let arr = [1,2,3,4,5,6];
arr.forEach(item => {
  if(item === 4) {
    return
  }
  console.log(item)
  // 1
  // 2
  // 3
  // 5
  // 6
})
```
##  跳出整体循环
* [forEach]{.red} 无法通过正常流程(如 [break]{.red})终止循环，但可通过抛出异常的方式实现终止循环
```js 代码
let arr = [1,2,3,4,5,6];
try {
  arr.forEach(item => {
    if(item === 4) {
      throw Error('终止循环');
    }
    console.log(item)
  })
} catch(error) {
  console.log(error)
}
```
打印如图：
![image.png](https://i.loli.net/2021/11/15/piuhXeOL7kIWs2f.png)
[:heart:筱何仔]{.label .danger}

