---
title: 一些Vue公共方法
top: false
cover: false
toc: true
mathjax: true
comments: true
date: 2021-02-09 17:10:33
password:
summary:
tags: 
- 前端
- Vue
categories:
- [前端, Vue]
id: 3
---
一些Vue公共方法
<!--more-->
# Common Functions
## vue路由跳转
```js 方法
// 第一步
// 方法放在common.js里
function toPage (path, query) {
  if (query) {
    this.$router.push({
      path,
      query
    })
  } else {
    this.$router.push({
      path
    })
  }
}
// 其他代码
···
···
// 记得导出
export {
  toPage
  ···
  ···
}
// 第二步
// 挂载在vue全局
import * as commonFunction from './common'
Vue.prototype.$toPage = commonFunction.toPage
// 第三步
// 页面中使用
// 无参数
this.$toPage(`/home`)
// 有参数
this.$toPage(`/AfterLhygOrderPay/AfterLhygOrderPayQrCode/${code}/${currentPaymentCode}/${item.paymentType}`)
// query
this.$toPage(`/customManagement/${code}/photos`, addCode)
this.$toPage(`/Worker/ContractTxt`, { id: item.id, contractCode: item.contractCode, roleName: this.roleName })
```
## 去除字符串空格
```js 示例 
// 去除字符串空格 用法同上
function trim (str, isGlobal = false) {
  if (!str) return ''
  str = str.replace(/(^\s+)|(\s+$)/g, '')
  if (isGlobal) str = str.replace(/\s/g, '')
  return str
}
```
[:heart:筱何仔]{.label .danger}