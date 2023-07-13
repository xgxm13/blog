---
title: Webpack基础
top: false
cover: false
toc: true
mathjax: true
comments: true
date: 2021-12-23 16:32:30
password:
summary:
tags:
- 前端
- Webpack
categories:
- [前端, Webpack]
id: 1
---
学习一下 [Webpack]{.red},顺便记录一下。
<!-- more -->
# 概念
* 本质上，[Webpack]{.red} 是一个用于现代 [JavaScript]{.red} 应用程序的 [静态模块打包工具]{.blue}。当 webpack 处理应用程序时，它会在内部从一个或多个入口点构建一个 依赖图(dependency graph)，然后将你项目中所需的每一个模块组合成一个或多个 bundles，它们均为静态资源，用于展示你的内容。

## 入口（entry）
* 入口起点(entry point) 指示 webpack 应该使用哪个模块，来作为构建其内部 依赖图(dependency graph) 的开始。默认值是 ./src/index.js，可以通过配置文件(webpack.config.js)修改。
```js webpack 
module.exports = {
  entry: './src/index.js',
}
```
## 输出(output)
* output 属性告诉 webpack 在哪里输出它所创建的 bundle，以及如何命名这些文件。主要输出文件的默认值是 ./dist/main.js，其他生成文件默认放置在 ./dist 文件夹中。
```js webpack 
const path = require('path') // 引入node.js的path模块
module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js', // 打包之后的名字
    path: path.resolve(__dirname, './dist'), // 打包之后的路径  注意要使用绝对路径 __dirname表示获取到当前webpack.congfig.js 的物理路径， './dist'表示基于这个物理路径解析到当前目录下的dist
    clean: true, // 打包时清除上次缓存
    assetModuleFilename: 'images/[contenthash][ext]' // 静态资源打包之后路径及命名 contenthash 根据文件内容生产哈希字符串 ext 扩展名 [] webpack系统自带的默认的生产文件名的方法
  },
}
```
## loader(加载器)
* webpack 只能理解 JavaScript 和 JSON 文件，这是 webpack 开箱可用的自带能力。loader 可以让webpack 解析其他类型的文件，并将它们转换为有效 模块。在 webpack 的配置中，loader 有两个属性：
test 属性，识别出哪些文件会被转换。
use 属性，定义出在进行转换时，应该使用哪个 loader。
```js webpack
const path = require('path') // 引入node.js的path模块
module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js', // 打包之后的名字
    path: path.resolve(__dirname, './dist'), // 打包之后的路径  注意要使用绝对路径 __dirname表示获取到当前webpack.congfig.js 的物理路径， './dist'表示基于这个物理路径解析到当前目录下的dist
    clean: true, // 打包时清除上次缓存
  },
  module: [
    {
      test: /\.(css|less)$/,
      use: ['style-loader', 'css-loader', 'less-loader'] // 后面先执行 逆序
    }
  ]
}
```
## 插件(plugin)
* webpack 编译器经过加工之后生成目标文件，比如生成js，css文件，webpack编译的过程需要一些工具来帮忙，这些工具就是插件。loader 用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。包括：打包优化，资源管理，注入环境变量等。
* 想要使用一个插件，你只需要 require() 它，然后把它添加到 plugins 数组中。多数插件可以通过选项(option)自定义。你也可以在一个配置文件中因为不同目的而多次使用同一个插件，这时需要通过使用 new 操作符来创建一个插件实例。
```js webpack
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, './dist'),
    clean: true,
    assetModuleFilename: 'images/[contenthash][ext]' 
  },
  mode: 'none',
  devtool: 'inline-source-map',
  plugins: [
    new HtmlWebpackPlugin({
      template: './index.html',
      filename: 'app.html',
      inject: 'body'
    })
  ],

  devServer: {
    static: './dist'
  },

  module: {
    rules: [
      {
        test: /\.png$/,  // 正则 //  .png . 需要转化 \.png  $ 表示以png作为拓展名的这种类型的文件
        type: 'asset/resource',
        generator: {
          // filename: 'images/test.png' //
          filename: 'images/[contenthash][ext]'
        }
      },
      {
        test: /\.svg$/,
        type: 'asset/inline',
      },
      {
        test: /\.txt$/, 
        type: 'asset/source'
      },
      {
        test: /\.jpg$/,
        type: 'asset',
        parser: {
          dataUrlCondition: {
            maxSize: 4*1024*1024
          }
        }
      }
    ]
  }
}
```
## 模式(mode)
* 通过选择 development, production 或 none 之中的一个，来设置 mode 参数，你可以启用 webpack 内置在相应环境下的优化。其默认值为 production。
```js webpack
module.exports = {
  mode: 'production',
};
```
## 浏览器兼容性(browser compatibility)
* Webpack 支持所有符合 ES5 标准 的浏览器（不支持 IE8 及以下版本）
## 环境(environment)
* Webpack 5 运行于 Node.js v10.13.0+ 的版本。
![image.png](https://s2.loli.net/2021/12/23/jh6sPfOzButMXrG.png)
[:heart:筱何仔]{.label .danger}