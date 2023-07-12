---
title: Mac Permission denied的解决方法
top: false
cover: false
toc: true
mathjax: true
comments: true
date: 2021-02-09 15:07:57
password:
summary:
tags:
- Mac
categories:
- [Mac]
id: 2
---
Mac Permission denied的解决方法
<!--more-->
这是因为[当前文件夹]{.red}的权限不够造成的，有一个比较管用的办法，一刀切解决问题
[当前目录]{.red}下打开[终端]{.red}
```java 示例 
sudo chmod -R 777 *
// 表示给这个项目的所有文件夹和文件夹下的文件都授予读写和可执行权限。
```
[:heart:筱何仔]{.label .danger}