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

Webpack 本身是不能识别样式资源的，所以需要借助 Loader 来帮助 Webpack 解析样式资源。找 Loader 都应该去官方文档中找到对应的 Loader后使用。官方文档找不到的话，可以从社区 Github 中搜索查询。基本流程都是一样的，下载依赖->在webpack.config.js中配置，还有创建资源 ，同时要记得引入。
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
注意官方文档中是```test: /\.css$/i```。
正则的i是修饰符，意思是不区分大小写，也就是你的文件是LESS还是less都能匹配上。但是不写i就要求一定得是less。
此外，官方文档的配置有的将```use```写为了```loader```。它们的区别是```loader:'xxx'```只能使用一个loader。可能需要我们自行将其修改为use。
3. 添加好CSS资源，并在入口文件```main.js```引入 Css 资源，Webpack才会对其打包。在```index.html```中引入打包后的js文件```../dist/main.js```，才能看到效果。
4. 运行指令```npx webpack```，打开 index.html 页面查看效果。

### 处理Less资源
less是css的一个预处理器，是 CSS 上的一种抽象层。 Less 是一种动态样式语言，将 CSS 赋予了动态语言的特性，如混合语法、变量，继承，运算， 函数，LESS 既可以在客户端上运行 (支持 IE 6+, Webkit, Firefox)，也可以在服务端运行 (借助 Node.js)。 

- less-loader：将less编译成css文件。

### 处理Sass、Scss、Styl资源
类似less。

## 处理图片资源
过去在 Webpack4 时，我们处理图片资源通过 file-loader 和 url-loader 进行处理：

- file-loader：将文件资源原封不动的输出出去，在过程中会将它编译成webpack能识别的资源
- url-loader：在file-loader的基础上，将小于某个大小的文件转化成base64，对图片资源做了一些优化。
现在 Webpack5 已经将两个 Loader 功能内置到 Webpack 里了，我们只需要简单配置即可处理图片资源。处理图片并不需要下载什么loader。

在```webpack.config.js```添加配置：
```javascript
{
  test: /\.(png|jpe?g|gif|webp)$/,
  type: "asset",
},
```
这里的```type: "asset"```即相当于使用了url-loader，能够帮助我们对图片进行处理。

各项配置好后再运行```npx webpack```，打开 index.html 页面查看输出资源情况，此时如果查看 dist 目录的话，会发现多了几张图片资源，也就是我们之前放到images文件夹然后引入了的几张图片，因为 Webpack 会将所有打包好的资源输出到 dist 目录下。
但为什么样式资源没有呢？因为经过 style-loader 的处理，样式资源打包到 main.js 里面去了，所以没有额外输出出来。

### 对图片资源进行优化
比如将小于某个大小的图片转化成 data URI 形式（Base64 格式）。
> 将图片转化为Base64 格式（DataUrl）的优势是会直接渲染图片，减少图片的请求数量，从而降低服务器压力。
> 图片转化为Base64 后体积会变大。图片原体积越大，转换成Base64后增加的体积越多，所以一般只会对小图片（5kb）进行这种处理。

在官方文档中搜索“asset”， 进入资源模块。可以看到很多种asset资源模块类型的多种形式。在通用资源类型中找到base64处理的相关配置，将其添加到处理图片原有的配置项里。如下：
```javascript
{
  test: /\.(png|jpe?g|gif|webp)$/,
  type: "asset",
  parser: {  // 添加的配置项
    dataUrlCondition: {
    maxSize: 10 * 1024 // 小于10kb的图片会被base64处理
    }
  }
},
```

配置完成后，就可以在打包时看到结果。注意他不会删除已经打包输出的文件，所以需要将上次打包生成的文件清空，再重新打包才有效果。

## 修改输出资源的名称和路径
到目前为止，可以看到所有打包好的资源都是同级的放在dist文件中，不利于很好的按js、img、css等文件类型整理文件。
在webpack.config.js文件中，output中的path——```path: path.resolve(__dirname, "dist")```，是所有文件的输出目录，所有打包的文件都在这；而filename——```filename: 'main.js'```是入口文件打包输出的文件名，只有入口文件打包出来放在这个目录。正确的修改方式如下：

- 将 js 文件输出到 static/js 目录中：```filename: "static/js/main.js"```
- 将图片文件输出到 static/imgs 目录中，在官方文档中搜索“asset”，找到“自定义输出文件名”，将```generator```配置项添加到图片资源的配置项中，也就是与图片配置项中的test、type、parser同级的位置。在dist文件夹中可以看到图片会有一个id，这个id对于webpack是一个哈希值，是唯一的：
```javascript
generator: {
  // 将图片文件命名 [hash:8][ext][query]
  // [hash:8]: hash值取8位
  // [ext]: 使用之前的文件扩展名
  // [query]: 添加之前的query参数
  filename: "static/imgs/[hash:8][ext][query]",
},
```

## 自动清空上次打包资源
只需在webpack.config.js文件中，output配置项中添加配置```clean: true```即可。作用就是在打包前把```path```文件夹整个清空再打包。
```javascript
output: {
  path: path.resolve(__dirname, "dist"),
  filename: "static/js/main.js",
  clean: true, // 自动将上次打包目录资源清空
},
```

## 处理字体图标资源
图标的三种使用方法：Unicode、Font class、Symbol。Font class用法简单，最常用。
先下载字体图标文件，再在自己的项目中添加字体图标资源并引入（注意字体文件路径需要修改）。比如使用阿里巴巴矢量图标库，需要添加的资源就有```src/fonts/iconfont.ttf```、```src/fonts/iconfont.woff```、```src/fonts/iconfont.woff2```、```src/css/iconfont.css```。
在webpack配置文件中的配置类似图片，依然是在generator中国呢改变输出名称。如下：
```javascript
{
  test: /\.(ttf|woff2?)$/,
  type: "asset/resource",
  generator: {
    filename: "static/media/[hash:8][ext][query]",
  },
},
```

+ 注意```type: "asset/resource"```和```type: "asset"```的区别：
1. ```type: "asset/resource"``` 相当于file-loader, 只会将文件转化成 Webpack 能识别的资源，其他不做处理。我们的字体文件不需要进行base64的转换，所以用这个。
2. ```type: "asset"``` 相当于url-loader, 将文件转化成 Webpack 能识别的资源，同时小于某个大小的资源会处理成 data URI 形式


## 处理音视频等其它资源
在处理字体图标资源基础上增加其他文件类型，统一处理即可。
```javascript
{
  test: /\.(ttf|woff2?|map4|map3|avi)$/,
  type: "asset/resource",
  generator: {
    filename: "static/media/[hash:8][ext][query]",
  },
}
```

## 处理js资源
js 资源 Webpack 不能已经处理了吗，为什么还要处理呢？
首先Webpack 对 js 处理是有限的，只能编译 js 中 ES 模块化语法，不能编译其他语法，导致 js 不能在 IE 等浏览器运行，因为ie不认识任何ES6语法，所以我们希望做一些兼容性处理；其次开发中，团队对代码格式是有严格要求的，我们不能由肉眼去检测代码格式，需要使用专业的工具来检测。

+ 针对 js 兼容性处理，我们使用 Babel 来完成；针对代码格式，我们使用 Eslint 来完成
+ 我们先完成 Eslint，检测代码格式无误后，在由 Babel 做代码兼容性处理

### Eslint
可组装的 JavaScript 和 JSX （react的语法就是JSX）检查工具。也就是说它是用来检测 js 和 jsx 语法的工具，可以配置各项功能。
> 原生支持react；要想支持vue，需要安点别的插件。

使用 Eslint关键是写 Eslint 配置文件，里面写上各种 rules 规则，将来运行 Eslint 时就会以写的规则对代码进行检查。  

+ 配置文件有很多种写法：
1. ```.eslintrc.*```：新建文件，位于项目根目录。这几个的区别在于配置格式不一样。
  + ```.eslintrc```
  + ```.eslintrc.js```
  + ```.eslintrc.json```
2. 在```package.json``` 中配置```eslintConfig```：不需要创建文件，在原有文件基础上写。

ESLint 会查找和自动读取它们，所以以上配置文件只需要存在一个即可。下面以```.eslintrc.js```为例：
```javascript
module.exports = {
  // 解析选项
  parserOptions: {},
  // 具体检查规则
  rules: {},
  // 继承其他规则
  extends: [],
  // ...
  // 其他规则详见：https://eslint.bootcss.com/docs/user-guide/configuring
};
```
1. parserOptions 解析选项。是
```javascript
parserOptions: {
  ecmaVersion: 6, // ES 语法版本
  sourceType: "module", // ES 模块化
  ecmaFeatures: { // ES 其他特性
    jsx: true // 如果是 React 项目，就需要开启 jsx 语法
  }
}
```
2. rules 具体规则
+ "off" 或 0 ：关闭规则
+ "warn" 或 1：开启规则，使用警告级别的错误：warn (不会导致程序退出)
+ "error" 或 2：开启规则，使用错误级别的错误：error (当被触发的时候，程序会退出)
```javascript
rules: {
  semi: "error", // 禁止使用分号
  'array-callback-return': 'warn', // 强制数组方法的回调函数中有 return 语句，否则警告
  'default-case': [
    'warn', // 要求 switch 语句中有 default 分支，否则警告
    { commentPattern: '^no default$' } // 允许在最后注释 no default, 就不会有警告了
  ],
  eqeqeq: [
    'warn', // 强制使用 === 和 !==，否则警告
    'smart' // https://eslint.bootcss.com/docs/rules/eqeqeq#smart 除了少数情况下不会有警告
  ],
}
```
更多规则详见：[规则文档](https://eslint.bootcss.com/docs/rules/)
3. extends 继承
开发中一点点写 rules 规则太费劲了，所以有更好的办法，继承现有的规则。现有以下较为有名的规则：

+ [Eslint 官方的规则](https://eslint.bootcss.com/docs/rules/)：eslint:recommended
+ [Vue Cli 官方的规则](https://yk2012.github.io/sgg_webpack5/base/javascript.html#_2-具体配置)：plugin:vue/essential
+ [React Cli 官方的规则](https://yk2012.github.io/sgg_webpack5/base/javascript.html#_2-具体配置)：react-app
```javascript
// 例如在React项目中，我们可以这样写配置
module.exports = {
  extends: ["react-app"],
  rules: {
    // 我们的规则会覆盖掉react-app的规则
    // 所以想要修改规则直接改就是了
    eqeqeq: ["warn", "smart"],
  },
};
```

要在webpack中使用，首先还是下载包：```
### Babel
JavaScript 编译器。主要用于将 ES6 语法编写的代码转换为向后兼容的 JavaScript 语法，以便能够运行在当前和旧版本的浏览器或其他环境中。





