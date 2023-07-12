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
## 对象按某个属性排序
```js codes
const arr = [
  {id: 1, name: '金刚', height: 120, weight: 180},
  {id: 2, name: '哥斯拉', height: 138, weight: 220},
  {id: 3, name: '机械哥斯拉', height: 130, weight: 190},
]
···
// 按某个属性（数字）排序
compare(prop) {
  return (a,b) => {
    let value1 = a[prop]
    let value2 = b[prop]
    return value1 - value2
  }
}

// 数组按某个属性（非数字）排序
function compare(prop) {
  return (a, b) => {
    let value1 = a?.[prop];
    let value2 = b?.[prop];
    // return (a.name + '').localeCompare(b.name + '')
    return value1.localeCompare(value2);
  };
}
console.log(arr.sort(compare('height')))  //返回对象height从小到大正向排序

```
* 下面內容 更新于2023年7月12日
## 防抖
```js codes
/**
 * @param {Function} func
 * @param {number} wait
 * @param {boolean} immediate
 * @return {*}
 */
export function debounce(func, wait, immediate) {
  let timeout, args, context, timestamp, result
  const later = function() {
    // 据上一次触发时间间隔
    const last = +new Date() - timestamp
    // 上次被包装函数被调用时间间隔 last 小于设定时间间隔 wait
    if (last < wait && last > 0) {
      timeout = setTimeout(later, wait - last)
    } else {
      timeout = null
      // 如果设定为immediate===true，因为开始边界已经调用过了此处无需调用
      if (!immediate) {
        result = func.apply(context, args)
        if (!timeout) context = args = null
      }
    }
  }
  return function(...args) {
    context = this
    timestamp = +new Date()
    const callNow = immediate && !timeout
    // 如果延时不存在，重新设定延时
    if (!timeout) timeout = setTimeout(later, wait)
    if (callNow) {
      result = func.apply(context, args)
      context = args = null
    }
    return result
  }
}
// 使用时 先引入
import { debounce } from '@/utils';
// 再调用
const handlePutOnShelves = debounce(() => {}, 100)

```
## 节流
```js codes
// 节流
export function throttle (fn, wait) {
  let callback = fn
  let timerId = null
  // 是否是第一次执行
  let firstInvoke = true
  function throttled () {
    let context = this
    let args = arguments
    // 如果是第一次触发，直接执行
    if (firstInvoke) {
      callback.apply(context, args)
      firstInvoke = false
      return
    }
    // 如果定时器已存在，直接返回。        
    if (timerId) {
      return
    }
    timerId = setTimeout(function () {
      // 注意这里 将 clearTimeout 放到 内部来执行了
      clearTimeout(timerId)
      timerId = null
      callback.apply(context, args)
    }, wait)
  }
  // 返回一个闭包
  return throttled
}
```
## 首字母大写
```js codes
// 首字母大写
export function titleCase(str) {
  return str.replace(/( |^)[a-z]/g, L => L.toUpperCase())
}
```
## 下划线转驼峰
```js codes
// 下划转驼峰
export function camelCase(str) {
  return str.replace(/_[a-z]/g, str1 => str1.substr(-1).toUpperCase())
}
```
## 根据身份证号码计算年龄
```js codes
/**
 * 根据身份证号码计算年龄
 * @param idCard 身份证
 * @returns 返回处理后的字符串
 */
export function getAgeAndSexByidCard(idCard:any) {
    let sex = '';
    // 计算性别
    if (idCard.substr(16, 1) % 2 === 1) {
        sex = '1'; //男
    } else {
        sex = '2'; //女
    }
    const birthYear = idCard.substring(6, 10);
    const monthBirth = idCard.substring(10, 12);
    const birthDay = idCard.substring(12, 14);
    // 获取当前年月日
    const myDate = new Date();
    const nowMonth = myDate.getMonth() + 1;
    const nowDay = myDate.getDay();
    // 计算年龄
    let age = myDate.getFullYear() - birthYear;
    if (nowMonth < monthBirth || (nowMonth === monthBirth && nowDay < birthDay)) {
        age -= 1;
    }
    let obj = {sex,age}
    return obj
};
```
## 动态计算table高度
```js codes
/**
 * 动态计算table高度
 * @param dom el-table结构的dom结构
 * @param num 页面除table结构之外的内容高度
 * @param tableHeight el-table的height值
 */
export function setTableHeight(dom:any, num:number, tableHeight:any) {
    let tableOffsetTop = dom.offsetTop + num
    let windowHeight = document.documentElement.clientHeight || document.body.clientHeight;
    tableHeight.value = windowHeight - tableOffsetTop;
}
// 使用时
// 动态设置table高度
function getTableHeight() {
  nextTick(() => {
    setTableHeight(document.querySelector('.tableOffsetTop'), 80, tableHeight);
  });
}
```
## 合并表格
```js codes
//从第一列开始合并，其中maxCol为从第一列开始数，要合并多少列
function rowSpanFromStart(index) {
  nextTick(() => {
    let tab = document.getElementById(`print-outbound-table-${index}`);
    let maxCol = 2,
      val,
      count,
      start;
    for (let col = maxCol - 1; col >= 0; col--) {
      count = 1;
      val = '';
      for (var i = 0; i < tab.rows.length; i++) {
        if (val == tab.rows[i].cells[col].innerHTML) {
          count++;
        } else {
          if (count > 1) {
            //合并
            start = i - count;
            tab.rows[start].cells[col].rowSpan = count;
            for (let j = start + 1; j < i; j++) {
              tab.rows[j].cells[col].style.display = 'none';
            }
            count = 1;
          }
          val = tab.rows[i].cells[col].innerHTML;
        }
      }
      if (count > 1) {
        //合并，最后几行相同的情况下
        start = i - count;
        tab.rows[start].cells[col].rowSpan = count;
        for (let j = start + 1; j < i; j++) {
          tab.rows[j].cells[col].style.display = 'none';
        }
      }
    }
  });
}
```
## 对象数组是否重复
```js codes
/**
 * @param arr 如：var arr = [{name: "123", age : 123}, {name:"1234432", age : 1234324}]
 * @param key1 如："name"
 * @param key2 如："age"
 * @retutns boolean: true：不存在； false：存在重复
 **/
function isRepeat(arr, key1, key2) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[i][key1] === arr[j][key1] && arr[i][key2] === arr[j][key2]) {
        return false;
      }
    }
  }
  return true;
}
// 使用时 
 let temp = isRepeat(list, 'materialCode', 'projectId')
```
## 根据状态返回中文
```js codes
// 根据状态返回中文
const getPlanStatus = (val) => {
  const enumStatus = {
    1: '已撤销',
    2: '待拣货',
    3: '待出库',
    4: '已出库',
    5: '待回库',
    6: '已回库',
  };
  return enumStatus[val];
};
```
[:heart:筱何仔]{.label .danger}