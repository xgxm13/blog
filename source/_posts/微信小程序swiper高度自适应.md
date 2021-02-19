---
title: 微信小程序swiper高度自适应
top: false
cover: false
toc: true
mathjax: true
comments: true
date: 2021-02-18 16:21:13
password:
summary:
tags:
  - 微信小程序
  - 前端
categories:
- [前端, 微信小程序]
id: 1
---
[微信小程序swiper]{.red} 高度自适应
<!--more-->
微信小程序自带的滑块视图容器 [swiper]{.kbd}{.red}不能通过设置[height]{.red}属性使高度自适应，这就导致我们代码开发中，经常出现子组件 [swiper-item]{.kbd}{.red}高度不一样的问题，影响页面美观。可以通过下面的方法解决。
* 在[data]{.red}里 声明 变量[clientHeight]{.red}
```js 方法
// 第一步
data () {
  return {
    clientHeight: 0
    ···
  }
}
```
*  页面 [swiper]{.kbd}{.red} 及  [scroll-view]{.kbd}{.red}添加高度属性[height]{.red}，高度值为[clientHeight]{.red}
```js 方法
// 第二步
<swiper @change="changeSwiper" :current="current" class="swiper" :style="'height:' + clientHeight + 'px'">
  <swiper-item v-for="(item, index) in tabsData" :key="index" class="swiperItem">
    <scroll-view scroll-y="true" :style="'height:' + clientHeight + 'px">
      <div class="mainContent">
        ··· // 其他代码
      </div>
    </scroll-view>
  </swiper-item>
</swiper>
```
* [onLoad()]{.red} 方法中获取高度 使用 [wx.getSystemInfo()]{.red}方法
```js 方法
// 第三步
onLoad () {
  var that = this
  wx.getSystemInfo({
    success: function (res) {
      that.clientHeight = res.windowHeight - 44 // 减去44 是因为有 44px 的标题高度
    }
  })
}
// wx.getSystemInfo() 成功时的回调如下
// 属性	类型	说明
// brand	string	设备品牌
// model	string	设备型号
// pixelRatio	number	设备像素比
// screenWidth	number	屏幕宽度，单位px
// screenHeight	number	屏幕高度，单位px
// windowWidth	number	可使用窗口宽度，单位px
// windowHeight	number	可使用窗口高度，单位px
// statusBarHeight	number	状态栏的高度，单位px
// language	string	微信设置的语言
// version	string	微信版本号
// system	string	操作系统及版本
// platform	string	客户端平台
// fontSizeSetting	number	用户字体大小（单位px）。以微信客户端「我-设置-通用-字体大小」中的设置为准
// SDKVersion	string	客户端基础库版本
// benchmarkLevel	number	设备性能等级（仅Android小游戏）。取值为：-2 或 0（该设备无法运行小游戏），-1（性能未知），>=1（设备性能值，该值越高，设备性能越好，目前最高不到50）
// environment	string	小程序当前运行环境
```

[:heart:筱何仔]{.label .danger}