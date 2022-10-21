---
title: webpack优化
updated: 2022-10-20 17:01:48
date: 2022-10-20 17:01:48
tags: [Webpack]
categories: [前端,Webpack]
---

## 提升开发体验——SourceMap
SourceMap（源代码映射）是一个用来生成源代码与构建后代码**一一映射**的文件的方案。它会生成一个 xxx.map 文件，里面包含源代码和构建后代码每一行、每一列的映射关系。当构建后代码出错了，会通过 xxx.map 文件，从构建后代码出错位置找到映射后源代码出错位置，从而让浏览器提示<span style="color:red">源代码</span>文件出错位置，帮助我们更快的找到错误根源。

开发时我们运行的代码是经过 webpack 编译后的，可以在```npm start```后打开浏览器的检查->源代码->static中找到main.js，可以看到所有 css 和 js 合并成了一个文件，并且多了其他代码，有很多注释，且每个模块都通过```eval()```函数包裹起来了，如下图。
![main.js](mainjs.png)
此时如果代码运行出错，提示的代码错误位置是编译后代码的位置，根本看不懂。一旦将来开发代码文件很多，那么很难去发现错误出现在哪里。所以我们需要更加准确的错误提示，来帮助我们更好的开发代码。
通过sourcemap生成的文件，是可以反编译，获取到源码的，生产环境下，一般关闭sourcemap。

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

查看[Webpack DevTool文档](https://webpack.docschina.org/configuration/devtool/)，它介绍了SourceMap的生成方式。配置方式叫```devtool```，开发者调试工具。SourceMap 的值有很多种情况：```eval```会把SourceMap文件打包到源代码的位置，用一个```eval()```函数包裹住；```eval-cheap-xxx```就只会构建每一行的映射关系，而不构建列的对应关系。。。performance下分别是初次构建和重新构建的速度；production是能否用于生产环境。
实际开发时我们只需要关注两种情况即可，在开发模式和生产模式对应的配置文件添加下面的配置，形如```devtool: "cheap-module-source-map"```。：

- 开发模式：使用```cheap-module-source-map```
  - 优点：打包编译速度快，只包含行映射
  - 缺点：没有列映射
```javascript
module.exports = {
  // 其他省略
  mode: "development",
  devtool: "cheap-module-source-map",
};
```

- 生产模式：使用```source-map```
  - 优点：又有行映射，又有列映射。
  - 缺点：打包编译速度更慢
```javascript
module.exports = {
  // 其他省略
  mode: "production",
   
};
```
<span style="color:red">生产模式需要关心列！因为生产模式的代码会经过压缩，变的只有一行；</span>如果不关心列，那么提示了错误依然找不到错误位置。


## 提升打包构建速度
### HotModuleReplacement（HMR/热模块替换）
HotModuleReplacement（HMR/热模块替换）：在程序运行中，替换、添加或删除模块，而无需重新加载整个页面。
> <span style="color:red">只有开发模式下有用！</span>生产模式下重新打包需要全部文件都参与，没法利用热模块替换。

开发时我们修改了其中一个模块代码，Webpack 默认会将所有模块全部重新打包编译，如果项目大、模块多，那速度就会很慢。所以我们需要做到修改某个模块代码，就只有这个模块代码需要重新打包编译，其他模块不变，这样打包速度就能很快。

这个配置在webpack5是默认打开的配置，在配置文件中通过```hot: false```关闭。
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
  // 希望当./js/count发生变化时，只接收到它自己的变化而不会全部更新：
  // module.hot.accept("./js/count")
  
  //  module.hot.accept()的第二个参数还可以是一个函数，接收到指定文件的变化后需要进行哪些工作：
  // 这个函数没什么事的话可以不写
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
上面这样写会很麻烦，有多少需要HMR的js就得写多少个。所以实际开发我们会使用其他 loader 来解决。比如[vue-loader](https://github.com/vuejs/vue-loader)，[react-hot-loader](https://github.com/gaearon/react-hot-loader)。

### Oneof
顾名思义就是只能匹配上一个 loader，剩下的就不匹配了。
打包时每个文件都会经过所有 loader 处理。虽然因为 test 正则原因实际没有处理上，但是都要过一遍，比较慢。比如这时候进来了一个js文件，它会遍历配置文件里的module->rules里的各种loader，从第一个开始；如果第一个不行，再看看下一个，还不行再看一个，以此类推。然而，就算遇到了一个可以处理的loader规则，它也不会停下来，而是会继续往下一个个loader看完。但实际上从我们的配置也可以看出来，每一种文件只有对应的一种loader去处理，它这么遍历是没有意义的。我们希望它一旦找到了对应的loader就不要再继续往后看了。

开发模式和生产模式的配置类似，下面以开发模式为例，只需要在rules里增加一个对象```{oneOf:[]}```再把所有loader放进去：
```javascript
rules: [
    // loader的配置。test里面是想要这个loader对那些文件生效。
    {
        // 每个文件只能被其中一个loader配置处理
        oneOf: [
            {
                test: /\.css$/, // 只检测.css文件，并对其使用下面的规则
                use: getStyleLoaders(),
            },
            // 其它loader，这里省略不写
        ]
    }
],
```

### Include/Exclude
- include：包含，只处理 xxx 文件
- exclude：排除，除了 xxx 文件以外其他文件都处理

开发时我们需要使用第三方的库或插件，所有文件都下载到 node_modules 中了。而这些文件是不需要编译可以直接使用的。所以我们在对 js 文件处理时，要排除 node_modules 下面的文件。

开发模式和生产模式的配置类似，下面以开发模式为例，
#
#
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