---
title: webpack优化
updated: 2022-10-20 17:01:48
date: 2022-10-20 17:01:48
tags: [Webpack]
categories: [前端,Webpack]
---

## 提升开发体验
### SourceMap
SourceMap（源代码映射）是一个用来生成源代码与构建后代码一一映射的文件的方案。
它会生成一个 xxx.map 文件，里面包含源代码和构建后代码每一行、每一列的映射关系。当构建后代码出错了，会通过 xxx.map 文件，从构建后代码出错位置找到映射后源代码出错位置，从而让浏览器提示源代码文件出错位置，帮助我们更快的找到错误根源。

开发时我们运行的代码是经过 webpack 编译后的，所有 css 和 js 合并成了一个文件，并且多了其他代码。此时如果代码运行出错那么提示代码错误位置我们是看不懂的。一旦将来开发代码文件很多，那么很难去发现错误出现在哪里。所以我们需要更加准确的错误提示，来帮助我们更好的开发代码。

不搞SourceMap的话大概长下面这个样子：
```javascript
/*
 * ATTENTION: The... ).
 ...
 * If y...n" (https://webpack.js.org/configuration/mode/).
 */
/******/ (() => { // webpackBootstrap
/******/ 	"use strict";
/******/ 	var __webpack_modules__ = ({

/***/ "./node_modules/css-loader/dist/cjs.js!./node_modules/less-loader/dist/cjs.js!./src/less/index.less":
/*!****************************************************************/
/***/ ((module, __webpack_exports__, __webpack_require__) => {

eval("__webpack_require_......");
/***/ }),
```

通过查看[Webpack DevTool文档](https://webpack.docschina.org/configuration/devtool/)可知，SourceMap 的值有很多种情况。但实际开发时我们只需要关注两种情况即可：

- 开发模式：cheap-module-source-map
  - 优点：打包编译速度快，只包含行映射
  - 缺点：没有列映射
```javascript
module.exports = {
  // 其他省略
  mode: "development",
  devtool: "cheap-module-source-map",
};
```

- 生产模式：source-map
  - 优点：包含行/列映射
  - 缺点：打包编译速度更慢
```javascript
module.exports = {
  // 其他省略
  mode: "production",
  devtool: "source-map",
};
```
## 提升打包构建速度
### HotModuleReplacement
HotModuleReplacement（HMR/热模块替换）：在程序运行中，替换、添加或删除模块，而无需重新加载整个页面。
开发时我们修改了其中一个模块代码，Webpack 默认会将所有模块全部重新打包编译，速度很慢。所以我们需要做到修改某个模块代码，就只有这个模块代码需要重新打包编译，其他模块不变，这样打包速度就能很快。

1. 基本配置
```javascript
module.exports = {
  // 其他省略
  devServer: {
    host: "localhost", // 启动服务器域名
    port: "3000", // 启动服务器端口号
    open: true, // 是否自动打开浏览器
    hot: true, // 开启HMR功能（只能用于开发环境，生产环境不需要了）
  },
};
```
此时 css 样式经过 style-loader 处理，已经具备 HMR 功能了。 但是 js 还不行。
2. JS配置
```javascript
// main.js
import count from "./js/count";
import sum from "./js/sum";
// 引入资源，Webpack才会对其打包
import "./css/iconfont.css";
import "./css/index.css";
import "./less/index.less";
import "./sass/index.sass";
import "./sass/index.scss";
import "./styl/index.styl";

const result1 = count(2, 1);
console.log(result1);
const result2 = sum(1, 2, 3, 4);
console.log(result2);

// 判断是否支持HMR功能
if (module.hot) {
  module.hot.accept("./js/count.js", function (count) {
    const result1 = count(2, 1);
    console.log(result1);
  });

  module.hot.accept("./js/sum.js", function (sum) {
    const result2 = sum(1, 2, 3, 4);
    console.log(result2);
  });
}
```
上面这样写会很麻烦，所以实际开发我们会使用其他 loader 来解决。比如[vue-loader]()open in new window, react-hot-loaderopen in new window。


### Oneof

### Include/Exclude

### Cache

### Thead


## 减少代码体积
### Tree Shaking


### Babel


### Image Minimizer


## 优化代码运行性能
### Code Split


### Preload/Prefetch


### Network Cache


### Core-js


### PWA