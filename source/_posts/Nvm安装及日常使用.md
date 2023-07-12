---
title: Nvm安装及日常使用
top: false
cover: false
toc: true
mathjax: true
comments: true
date: 2021-02-22 15:58:50
password:
summary:
tags:
- [笔记]
categories:
- [笔记]
id: 2
---
node.js的版本管理工具Nvm的安装及日常使用
<!--More-->
* 项目中经常遇到不同项目需要不同的 [node.js]{.red}版本或者你本地node版本与客户现有系统的 [node.js]{.red}版本相差较大等情况，我们肯定不可能卸载现有的再装客户需要的，这时 [Nvm]{.red}就派上了用场。通过 [Nvm]{.red}，我们可以来回切换不同版本的[node.js]{.red}，十分方便。
# Nvm的安装
## mac安装  
   * [官方md:https://github.com/nvm-sh/nvm/blob/master/README.md](https://github.com/nvm-sh/nvm/blob/master/README.md) (多打开几次，比较慢)
   
```js 安装 
// 命令行代码
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash
// 或者
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash
```
* 安装完成后关闭终端，重新打开终端输入 [nvm]{.red} 验证一下是否安装成功，当出现 [Node Version Manager]{.red}时，说明已安装成功。如下图：（单击可放大）
![1613984786965.jpg](https://i.loli.net/2021/02/22/JFC4bwHGuinTs2X.jpg)
## windows安装
* 下载[nvm-windows]{.blue} [https://github.com/coreybutler/nvm-windows](https://github.com/coreybutler/nvm-windows) (多打开几次，比较慢)
# Nvm的简单使用
```js 简单使用
// 简单使用
nvm list  // 查看已安装所有node版本
nvm install 10.15.0 // 安装指定版本node
nvm uninstall 10.15.0 //卸载指定版本的node
nvm use 10.15.0 // 切换node版本
nvm alias default 10.15.0 // 指定默认版本号
```
* [nvm list]{.blue} 
![list.jpg](https://i.loli.net/2021/02/22/uZTEmztkdjBQneP.jpg)
* [nvm use [version]]{.blue}
![use.jpg](https://i.loli.net/2021/02/22/KdJIkEB9LfWpT5X.jpg)
* [nvm install [version]]{.blue}
![install.jpg](https://i.loli.net/2021/02/22/EX3NHCxZ7lFT4dg.jpg)
* [nvm uninstall [version]]{.blue} (不能卸载正在使用的版本，会报 nvm: Cannot uninstall currently-active node version, v6.17.1 (inferred from 6.17.1).)
![uninstall.jpg](https://i.loli.net/2021/02/22/oPNZk9ywujIS2le.jpg)
* [nvm alias default [version]]{.blue}
![default.jpg](https://i.loli.net/2021/02/22/zcnAdFDIk2ZpamN.jpg)
---
日常这几个命令已经够用了。
[:heart:筱何仔]{.label .danger}