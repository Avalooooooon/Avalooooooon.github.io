---
title: Webpack教程
updated: 2022-10-13 12:56:02
date: 2022-10-13 12:56:02
tags: [Webpack]
categories: [前端,Webpack]
---

> Webpack是一个静态资源打包工具。以一个或多个文件作为打包的入口，将我们整个项目所有文件编译组合成一个或多个文件输出出去。
> “打包”，就是编译，把浏览器不识别的语法编译成可以识别的语法。输出的文件就是编译好的文件，就可以在浏览器段运行了。
> 我们将这种 Webpack 输出的文件叫做 bundle。
[Webpack在线课件](https://yk2012.github.io/sgg_webpack5/base)

## Webpack作用
开发时，我们会使用框架（React、Vue），ES6 模块化语法，Less/Sass 等 css 预处理器等语法进行开发。这样的代码要想运行，必须经过编译，编译成浏览器能识别的 JS、Css 等语法，才能运行。 
所以我们需要打包工具帮我们把框架什么的语法编译成浏览器可以识别的语法，最终才能在浏览器中运行。
打包工具还能压缩代码、做兼容性处理、提升代码性能等。
除了最常用的Webpack外，比较常见的打包工具还有Grunt、Gulp、Parcel、Rollup、Vite等。

Webpack 本身是不能识别样式资源的，所以需要借助 Loader 来帮助 Webpack 解析样式资源。找 Loader 都应该去官方文档中找到对应的 Loader后使用。官方文档找不到的话，可以从社区 Github 中搜索查询。
[Webpack 官方 Loader 文档](https://webpack.docschina.org/loaders/)
> 不过官网可能存在一个问题，在“快速开始”的第一步中，```npm install --save-dev xxx```中的xxx可能未完整包含所需要的依赖。所以在我们使用的时候还需要观察官网在```webpack.config.js```中的module - rules - use字段中用了哪些loader，就去下载这些loader。
> 或者根据```npx webpack```后的报错里```Can't resolve xxx```，无脑  去下载这里提到的loader就行。

<span style="color:red">想要webpack打包资源，必须得在入口文件中引入该资源。</span>所以，想要处理什么资源，就要先创建这个资源，并且要在入口文件中import引入，它才会被打包。有些类型的文件，比如css，直接打包会报错，因为webpack不能处理这种资源。这时候就要去配置loader，让它可以处理这些资源。去官方文档下载依赖、进行依赖配置。

在```index.html```中<span style="color:red">引入打包后的js文件</span>，才能看到效果。

运行指令```npx webpack```，打开 index.html 页面查看效果。

## 开始使用
Webpack 本身功能是有限的，有两种模式:
开发模式：仅能编译 JS 中的 ES Module 语法。在项目开发过程中使用的模式。
生产模式：除能编译 JS 中的 ES Module 语法外，还能压缩 JS 代码。代码要部署上线时使用的模式。
所以它本身能处理的文件只有JS文件，样式文件遇到就会报错，处理不了。所以需要学习Webpack的其他配置来让它的功能变得强大。

在项目根目录中，如果没有package.json，则需要执行```npm init -y```命令生成package.json并对其进行初始化。此时会生成一个基础的package.json文件。执行```npm i webpack webpack-cli -D```下载依赖（这里的```-D```是把依赖安装到开发依赖的意思）。

+ 执行```npx webpack ./src/main.js --mode=development```开始打包，该命令指定环境为开发环境。

  - 命令```npx webpack```: 是用来运行本地安装 Webpack 包的。
  - 命令```./src/main.js```: 指定 Webpack 从哪个文件作为入口开始打包。不但会打包入口文件main.js，还会将其依赖也一起打包进来。
  - 命令```--mode=xxx```：指定模式（环境）。

+   打包完成后会生成一个```dist```目录，打包出来的文件会输出到这个文件夹中。打开```main.js```，可以发现webpack把一些ES6的语法编译成了\_\_webpack_modules\_\_、\_\_webpack_exports\_\_等变量。此外还可以发现，每一个.js文件资源都被一个```eval()```函数包起来了。

  > 需要注意的是，在开发环境中webpack只能编译ES6的**模块化**语法，```...```和箭头函数等其他语法并不能编译。

+   编译完成后，把入口文件```index.html```中引入的src中的```main.js```换成dist文件夹中的```main.js```再运行，打开控制台，会发现没有报错了。（直接运行src中的main.js，会报错：```Cannot use import statement outside a module```，因为导入文件模块是es6模块化语法，而浏览器并不支持es6语法。）

+ 上面的例子是在开发环境下进行编译。如果在生产环境中进行打包，指令变为```npx webpack ./src/main.js --mode=production```。此时webpack也会把结果输出到dist文件夹中，但会进行压缩，生成非常简单的代码。

## 基本配置
- 5大核心概念如下：
1. entry（入口）：指示 Webpack 从哪个文件开始打包。Webpack 会以一个或多个文件作为入口。
  > entry里的路径需要用<span style="color:red">相对路径</span>。

2. output（输出）：指示 Webpack 打包完的文件输出到哪里去，如何命名等。如果不指定也会有一个默认值，但不推荐，因为将来大概率需要对打包后的文件进行一些操作。
  > 注意需要指定输出的两个细节：```path```和```filename```。 分别是输出路径和输出名称。
  
  > output里的路径需要用<span style="color:red">绝对路径</span>。需要用到node.js的核心模块：```path```，该模块专门用来处理路径问题。
  > 使用path模块的一个方法：```path.resolve()```，该方法返回一个绝对路径。```__dirname```变量是node环境下的一个变量，表示当前文件所在的文件夹的路径，可以直接使用。

3. loader（加载器）：webpack 本身只能处理 js、json 等资源，样式、图片等其他资源需要借助 loader，Webpack 才能解析。
  > 在```webpack.config.js```中加载器的名字叫```module```，里面写着```rules```。
  > rules是一个数组，其内部要写的是loader的规则。

4. plugins（插件）：扩展 Webpack 的功能。是一个数组。

5. mode（模式）：主要由两种模式：开发模式development；生产模式production。
  - 开发模式主要在开发过程中使用，代码能运行即可。这个模式下我们主要做两件事：
    1. 编译代码，使浏览器能识别运行：webpack 本来只能处理js资源，然而开发时还有样式资源、字体图标、图片资源、html 资源等，webpack 默认都不能处理这些资源，所以要加载配置来编译这些资源。
    2. 代码质量检查，树立代码规范：可以借助一些工具，如ESLint，提前检查代码的一些隐患。提前检查代码规范和格式，统一团队编码风格，让代码更优雅美观。
  
  - 而生产模式就需要对代码进行各种优化。

### 配置文件
<span style="color:red">配置文件：```webpack.config.js```一定位于项目的根目录下！</span>由于Webpack 是基于 Node.js 运行的，所以采用 Common.js 模块化规范：```module.exports = {}```。基本格式如下：
```javascript
const path = require("path")  // nodejs核心模块
module.exports = {
    // 入口。注意是相对路径。
    entry: "./src/main.js",
    // 输出。注意是绝对路径。
    output: {
        path: path.resolve(__dirname, "dist"),
        filename: 'main.js'
    },
    // 加载器
    module: {
        rules: [
            // loader的配置
        ],
    },
    // 插件
    plugins: [
        // plugin的配置
    ],
    // 模式
    mode: "development",  // 此处指定的是开发环境。
};
```
写好配置文件后，在控制台运行webpack。命令由```npx webpack ./src/main.js --mode=development```变成了```npx webpack```。
```npx webpack```命令会去当前运行所在的目录中找```webpack.config.js```文件，读取里面的配置，按照里面的配置执行相应的打包操作。
类似```npx webpack ./src/main.js --mode=development```，如果在```npx webpack```后面加了指令，则会按加的指令执行相应的操作。
总之就是，如果有配置文件，就```npx webpack```；没配置文件就手动加指令，用```npx webpack ./src/main.js --mode=development```。

## 处理CSS资源
1. 下载CSS Loader及其它处理CSS需要用到的loader：```npm i css-loader style-loader -D```。这里具体下载哪几个需要去官网查看其配置文件中module - rules - use都有什么。
  - css-loader：先执行。将css资源编译成commonjs的模块到js中，即负责将 Css 文件编译成 Webpack 能识别的模块。
  - style-loader：在css-loader后执行。会动态创建一个 Style 标签，里面放置 Webpack 中 Css 模块内容。需要先把css打包到js中。
  下载好后样式就会以 Style 标签的形式在页面上生效。
2. 在配置文件中的配置如下：
```javascript
const path = require("path")  // nodejs核心模块
module.exports = {
    entry: "./src/main.js",  // 入口。注意是相对路径。
    output: {
        path: path.resolve(__dirname, "dist"),  // 输出。注意是绝对路径。
        filename: 'main.js'
    },
    // 加载器
    module: {
        rules: [
            // loader的配置。test里面是想要这个loader对那些文件生效。
            {
                test: /\.css$/, // 只检测.css文件，并对其使用下面的规则
                use: [   
                    // 使用什么loader去处理。执行顺序是从后到前。
                    "style-loader",  // 通过动态创建style标签使样式生效。
                    "css-loader", // 把css打包到js中。
                ],
            },
        ],
    },
    plugins: [],
    mode: "development",  // 此处指定的是开发环境。
};
```
3. 添加好CSS资源，并在入口文件```main.js```引入 Css 资源，Webpack才会对其打包。在```index.html```中引入打包后的js文件```../dist/main.js```，才能看到效果。
4. 运行指令```npx webpack```，打开 index.html 页面查看效果。

## 处理Less资源

## 处理Sass和Scss资源

## 处理Styl资源



















