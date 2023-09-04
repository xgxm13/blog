---
title: Css 预处理语言Sass、Less、Stylus区别
top: false
cover: false
toc: true
mathjax: true
comments: true
date: 2023-09-04 16:15:39
password:
summary:
tags: 
- 前端
- CSS
categories: 
- [前端, CSS]
id: 1
---
* CSS 预处理器框架，分别是 Sass、Less 、Stylus。CSS预处理器为CSS提供了更多的更加灵活的可编程性。增加了变量、嵌套、运算、混入(Mixin)、继承、颜色处理、函数等特性。
<!--more-->
参考CSDN [吴小花的博客](https://blog.csdn.net/weixin_39089928/article/details/88379564)

# 背景介绍
* [Sass：]{.red}2007年诞生，最早也是最成熟的CSS预处理器，拥有ruby社区的支持和compass这一最强大的css框架。目前受LESS影响，已经进化到了全面兼容CSS的SCSS（SCSS 需要使用分号和花括号而不是换行和缩进）。
* [Less：]{.red}2009年出现，受SASS的影响较大，但又使用CSS的语法，让大部分开发者和设计师更容易上手，在ruby社区之外支持者远超过SASS。其缺点是比起SASS来，可编程功能不够。不过优点是简单和兼容CSS，反过来也影响了SASS演变到了SCSS的时代，著名的Twitter Bootstrap就是采用LESS做底层语言的
* [Stylus：]{.red}2010年产生，来自Node.js社区。主要用来给Node项目进行CSS预处理支持，在此社区之内有一定支持者，在广泛的意义上人气还完全不如SASS和LESS。

# 语法区别
* Sass 和 Less 都使用的是标准的 CSS 语法， Sass 同时也支持老的语法，就是不包含花括号和分号的方式。
* Stylus 支持的语法要更多样性一点，它默认使用 .styl 的文件扩展名
下面是 Stylus 支持的语法：
```js codes
/* style.styl */
h1 {
  color: #0982C1;
}
 
/* omit brackets */
h1
  color: #0982C1;
 
/* omit colons and semi-colons */
h1
  color #0982C1
```

# 变量的定义
* Sass 的变量必须是 $ 开始，然后变量名和值使用冒号隔开，跟 CSS 的属性一致：
```js  codes
//定义
$mainColor: #0982c1;
//使用：
color: $mainColor;
```
*  Less 的变量名使用 @ 符号开始：
```js  codes
//定义
@mainColor: #0982c1;
//使用：
color: @mainColor;
```
* Stylus 对变量名没有任何限定，你可以是 $ 开始，也可以是任意的字符(在 Stylus 的变量名不要用 @ 开头)，而且与变量值之间可以用冒号、空格隔开
```js  codes
//定义
mainColor = #0982c1
$mainColor = #0982c1
 
//使用
color mainColor
```

# 嵌套
Sass、Less、Stylus 都可以嵌套

# mixins混入
* Sass 的混入语法
```js codes
//定义
@mixin error($borderWidth: 2px) {
  border: $borderWidth solid #F00;
}
//使用 
@ include error(); 
 
@ include error(5px); 
```
* Less 的混入语法
```js codes
//定义
.error(@borderWidth: 2px) {
  border: @borderWidth solid #F00;
}
 
//引用
.error(); 
 
.error(5px); 
```
* Stylus 的混入语法
```js codes 
//定义
error(borderWidth= 2px) {
  border: borderWidth solid #F00;
}
 
//使用
  error(); 
 
  error(5px); 
```

# 继承
* 在 Sass 和 Stylus 我们可以这样写：
```js codes
.block {
  padding: 2px;
}
 
p {
  @extend .block; /* Inherit styles from '.block' */
}
```
* less写法
```js codes
.block {
  padding: 2px;
}
 
p {
  .block; 
}
```
# 导入 import
* 导入需要多次的 HTTP 请求。但是在 CSS 预处理器中的导入操作则不同，它只是在语义上包含了不同的文件，但最终结果是一个单一的 CSS 文件，如果你是通过 @ import "file.css"; 导入 CSS 文件，那效果跟普通的 CSS 导入一样。
* 注意：导入文件中定义的混入、变量等信息也将会被引入到主样式文件中，因此需要避免它们互相冲突。

reset.css:

```js codes
/* file.{type} */
body {
  background: #EEE;
}

```

main.xxx
```js codes

@ import "reset.css";
@ import "file.{type}";
 
p {
  background: #0982C1;
}

```
最终生成的 CSS: 
```js codes
@ import "reset.css";
body {
  background: #EEE;
}
p {
  background: #0982C1;
}
```
# 颜色函数
CSS 预处理器一般都会内置一些颜色处理函数用来对颜色值进行处理，例如加亮、变暗、颜色梯度等。
* Sass
```js codes
lighten($color, 10%); /* 比$color更亮10% */
darken($color, 10%);  /* 更黑 */
 
saturate($color, 10%);   /* returns a color 10% more saturated 饱和的 深的;浓的 than $color */
desaturate($color, 10%); /* returns a color 10% less saturated than $color */
 
grayscale($color);  /* returns grayscale 灰度 of $color */
complement($color); /* returns complement 补足 color of $color */
invert($color);     /* returns inverted 反向的，倒转的，颠倒的 color of $color */
 
mix($color1, $color2, 50%); /* mix $color1 with $color2 with a weight of 50% */
```
* Less
```js codes
lighten(@color, 10%); /* returns a color 10% lighter than @color */
darken(@color, 10%);  /* returns a color 10% darker than @color */
 
saturate(@color, 10%);   /* returns a color 10% more saturated than @color */
desaturate(@color, 10%); /* returns a color 10% less saturated than @color */
 
spin(@color, 10);  /* returns a color with a 10 degree larger in hue than @color */
spin(@color, -10); /* returns a color with a 10 degree smaller hue than @color */
 
mix(@color1, @color2); /* return a mix of @color1 and @color2 */
```
* Stylus
```js codes
lighten(color, 10%); /* returns a color 10% lighter than 'color' */
darken(color, 10%);  /* returns a color 10% darker than 'color' */
 
saturate(color, 10%);   /* returns a color 10% more saturated than 'color' */
desaturate(color, 10%); /* returns a color 10% less saturated than 'color' */
```
# 运算符
* 可以直接在 CSS 预处理器中进行样式的计算
```js codes
body {
  margin: (14px/2);
  top: 50px + 100px;
  right: 100px - 50px;
  left: 10 * 10;
}
```

[:heart:筱何仔]{.label .danger}