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
通过sourcemap生成的文件是可以反编译获取到源码的，生产环境下一般关闭sourcemap。

不搞SourceMap的话main.js大概长下面这个样子：
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
  // module.hot.accept()的第二个参数还可以是一个函数，接收到指定文件的变化后需要进行哪些工作：
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

开发模式和生产模式的配置类似，下面以开发模式为例，只需要在rules里增加一个对象```{oneOf:[]}```再把所有loader放进[]：
```javascript
rules: [
    {
        // 每个文件只能被其中一个loader配置处理
        oneOf: [
            {
                test: /\.css$/, 
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
> 这两个配置项只能出现一个！且只针对js。因为就算是引入了css，也希望这些引入的样式和我们自己的样式打包在一起。

开发时我们需要使用第三方的库或插件，所有文件都下载到 node_modules 中，而这些文件已经编译过了，直接import使用就行，不需要再重复编译了。所以我们在对 js 文件处理时，要排除 node_modules 下面的文件，只处理src下面的文件，这样速度会更快。

开发模式和生产模式的配置类似，下面以开发模式的配置文件```webpack.dev.js```为例。因为主要涉及到的是js文件，所以在babel和eslint相关的配置中进行配置：
```javascript
{
    test: /\.js$/,
    // exclude: /node_modules/, // 排除node_modules代码不编译
    include: path.resolve(__dirname, "../src"), // 这句和上面那句二选一
    loader: "babel-loader",
},
...
plugins: [
    new ESLintWebpackPlugin({
      // 指定检查文件的根目录
      context: path.resolve(__dirname, "../src"),
      exclude: "node_modules", // 这就是默认值
    }),
    new HtmlWebpackPlugin({
      // 以 public/index.html 为模板创建文件
      // 新的html文件有两个特点：1. 内容和源文件一致 2. 自动引入打包生成的js等资源
      template: path.resolve(__dirname, "../public/index.html"),
    }),
  ],
```

### Cache
用来对Eslint检查和Babel编译结果进行缓存。
因为开发的时候js文件占的比重是最大的，而且每次打包时 js 文件都要经过 Eslint检查+Babel编译，处理样式什么的都比较快，整个webpack打包输出的过程就慢在这里；这也是优化常常针对js文件进行的原因。可以缓存之前的Eslint检查和Babel 编译结果，这样第二次打包时只会针对修改的文件重新做Eslint检查和Babel编译，没改动的文件直接用缓存的Eslint检查和Babel编译结果。速度就会更快了。
使用方式如下。在开发模式下改变配置不好查看结果，所以一般在```webpack.prod.js```中进行操作；开发模式和生产模式的配置方式相同。
首先来看babel。在options中填写相关选项。这里关闭缓存文件的压缩，因为压缩是需要时间的，并且代码正式上线的时候用不上这些缓存文件：
```javascript
{
    test: /\.js$/,
    // exclude: /node_modules/, // 排除node_modules代码不编译
    include: path.resolve(__dirname, "../src"), // 也可以用包含
    loader: "babel-loader",
    options: {
        cacheDirectory: true, // 开启babel编译缓存
        cacheCompression: false, // 缓存文件不要压缩
    },
},
```
再运行```npm run build```，可以在node_modules文件夹看到额外生成了一个.cache文件夹，里面就是缓存文件。
然后是eslint相关的：
```javascript
new ESLintWebpackPlugin({
    // 指定检查文件的根目录
    context: path.resolve(__dirname, "../src"),
    exclude: "node_modules", // 默认值
    cache: true, // 开启缓存
    // 指定 缓存目录
    cacheLocation: path.resolve(
        __dirname,
        "../node_modules/.cache/.eslintcache"
    ),
}),
```
### Thead（多进程打包）
多进程打包：开启电脑的多个进程同时干一件事，速度更快。
> 需要注意：请仅在特别耗时的操作中使用，因为每个进程启动就有大约为 600ms 左右开销。
> 我们目前打包的内容都很少，所以因为启动进程开销原因，使用多进程打包实际上会显著的让我们打包时间变得很长。

当项目越来越庞大时，打包速度越来越慢，甚至于需要一个下午才能打包出来代码。这个速度是比较慢的。想要继续提升打包速度，其实就是要提升 js 的打包速度，因为其他文件都比较少；而对 js 文件处理主要就是 eslint 、babel、Terser （生产模式下js代码会自动压缩的原理就是webpack内部用了这个插件，只要开启了生产模式就会自动应用）三个工具，所以我们要提升它们的运行速度。
目前都是使用一个进程先后进行eslint 、babel、Terser ，速度较慢；可以开启多进程同时处理 js 文件，这样速度就比之前的单进程打包更快了。

启动进程的数量就是我们 CPU 的核数。Thead的使用方法如下：
1. 获取 CPU 的核数(每个电脑都不一样)：在```webpack.prod.js```配置：
```javascript
// nodejs核心模块，直接使用
const os = require("os");
// cpu核数
const threads = os.cpus().length;
```
2. 下载包：```npm i thread-loader -D```
3. 使用。Thead loader需要放在我们要处理的loader前面，放在了babel-loader前；eslint也需要配置，直接通过```threads, // 开启多进程```开启多进程即可；最后还有一个压缩js的工作，需要我们手动引入一个插件，这个插件webpack已经内置了，所以无需下载，直接使用即可，还是在之前使用插件的地方引入，也可以像下面的例子那样写进```optimization:{}```中：
```javascript
const TerserPlugin = require("terser-webpack-plugin");
// ...
{
    test: /\.js$/,
    // exclude: /node_modules/, // 排除node_modules代码不编译
    include: path.resolve(__dirname, "../src"), // 这句和上面那句二选一
    use: [
        {
            loader: "thread-loader", // 开启多进程
            options: {
                workers: threads, // 数量
            },
        },
        {
            loader: "babel-loader",
            options: {
                // presets: ['@babel/preset-env'],
                cacheDirectory: true, // 开启babel编译缓存
                // cacheCompression: false, // 缓存文件不要压缩

            },
        },
    ],
},
// ...
plugins: [
    new ESLintWebpackPlugin({
      // 指定检查文件的根目录
      context: path.resolve(__dirname, "../src"),
      exclude: "node_modules", // 默认值
      cache: true, // 开启缓存
      // 缓存目录
      cacheLocation: path.resolve(
        __dirname,
        "../node_modules/.cache/.eslintcache"
      ),
      threads, // 开启多进程
    }),
    // new HtmlWebpackPlugin({
    // 提取css成单独文件
    new MiniCssExtractPlugin({
      // 定义输出文件名和目录
      filename: "static/css/main.css",
    }),
    // css压缩
    // new CssMinimizerPlugin(),
  ],
  optimization: {
    // 压缩的操作
    minimize: true,
    minimizer: [
      // 压缩css：css压缩也可以写到optimization.minimizer里面，效果一样的
      new CssMinimizerPlugin(),
      // 压缩js：当生产模式会默认开启TerserPlugin，但是我们需要进行其他配置，就要重新写了
      new TerserPlugin({
        parallel: threads // 开启多进程
      })
    ],
  },
```
  > 只要写了minimizer都会使Terser失效，必须重新配置！
  > js在开发模式中不需要压缩，所以无需处理。

## 减少代码体积
### Tree Shaking
Tree Shaking 是一个术语，通常用于描述移除 JavaScript 中的没有使用上的代码。<span style="color:red">它依赖 ES Module。</span>

开发时我们定义了一些工具函数库，或者引用第三方工具函数库或组件库。如果没有特殊处理的话我们打包时会引入整个库，但是实际上可能我们可能只用上极小部分的功能。这样将整个库都打包进来，体积就太大了。我们希望用上了什么，就打包什么。

Webpack 已经默认开启了这个功能，无需其他配置。

### Babel
```@babel/plugin-transform-runtime```: 禁用了 Babel 自动对每个文件的 runtime 注入，而是引入 ```@babel/plugin-transform-runtime```并且使所有辅助代码从这里引用。

Babel 为编译的每个文件都插入了**辅助代码**，使代码体积过大！Babel 对一些公共方法使用了非常小的辅助代码，比如``` _extend```。默认情况下会被添加到每一个需要它的文件中。我们应该将这些辅助代码作为一个独立模块，谁要用谁就引入，从而避免重复引入。

使用方法如下：
1. 下载包：```npm i @babel/plugin-transform-runtime -D```
2. 配置，在配置文件的babel-loader中添加：
```javascript
{
  loader: 'babel-loader',
  options: {
    // presets: ['@babel/preset-env'],
    cacheDirectory: true, // 开启babel编译缓存
    cacheCompression: false, // 缓存文件不要压缩
    plugins: ["@babel/plugin-transform-runtime"], // 减少代码体积
  }
},
```


### Image Minimizer
```image-minimizer-webpack-plugin```: 用来压缩图片的插件

开发如果项目中引用了较多图片，那么图片体积会比较大，将来请求速度比较慢。可以对图片进行压缩，减少图片体积。
> 注意：如果项目中图片都是在线链接，那么就不需要了。<span style="color:red">它本地项目静态图片才需要进行压缩。</span>

使用方法如下：
1. 下载包：```npm i image-minimizer-webpack-plugin imagemin -D```
  还有剩下包需要下载，有两种模式：
  
  - 无损压缩：```npm install imagemin-gifsicle imagemin-jpegtran imagemin-optipng imagemin-svgo -D```
  - 有损压缩：```npm install imagemin-gifsicle imagemin-mozjpeg imagemin-pngquant imagemin-svgo -D```
  [无损和有损压缩的区别](https://baike.baidu.com/item/无损、有损压缩)
2. 配置：以无损压缩配置为例，因为这是个插件，所以还是new的方法配置；又因为所有的压缩方法可以放在一起，就放在optimization中进行配置：
```javascript
const ImageMinimizerPlugin = require("image-minimizer-webpack-plugin");
// ...
// 压缩图片
new ImageMinimizerPlugin({
    minimizer: {
      implementation: ImageMinimizerPlugin.imageminGenerate,
      options: {
        plugins: [
          ["gifsicle", { interlaced: true }],
          ["jpegtran", { progressive: true }],
          ["optipng", { optimizationLevel: 5 }],
          [
            "svgo",
            {
              plugins: [
                "preset-default",
                "prefixIds",
                {
                  name: "sortAttrs",
                  params: {
                    xmlnsOrder: "alphabetical",
                  },
                },
              ],
            },
          ],
        ],
      },
    },
  }),
```
3. 打包时可能会出现报错：
```bash
Error: Error with 'src\images\1.jpeg': '"C:\Users\86176\Desktop\webpack\webpack_code\node_modules\jpegtran-bin\vendor\jpegtran.exe"'

Error with 'src\images\3.gif': spawn C:\Users\86176\Desktop\webpack\webpack_code\node_modules\optipng-bin\vendor\optipng.exe ENOENT
```

我们需要安装两个文件到 node_modules 中才能解决：

  - jpegtran.exe：需要复制到 node_modules\jpegtran-bin\vendor 下面
    > [jpegtran 官网地址](http://jpegclub.org/jpegtran/)
  - optipng.exe：需要复制到 node_modules\optipng-bin\vendor 下面
    > [optipng 官网地址](https://optipng.sourceforge.net)

## 优化代码运行性能
### Code Split


### Preload/Prefetch


### Network Cache


### Core-js


### PWA