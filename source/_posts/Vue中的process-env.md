---
title: Vue中的process.env
tags:
  - Vue
categories:
  - 前端
  - Vue
updated: 2023-03-20 16:02:52
date: 2023-03-20 16:02:52
---



参考：https://www.cnblogs.com/crcce-dncs/p/14175275.html

## node环境变量process.env

process对象是一个全局变量，提供了有关当前Node.js进程的信息并对其进行控制。作为全局变量，它始终可供 Node.js 应用程序使用，无需使用 require()。它也可以使用 require() 显式地访问：`const process = require('process')`。

可以直接使用。例如在控制台中，可以先输入`node`进入node环境，再输入`process`后即可打印出以下信息。可以看到process是node的全局变量，并且process有env这个属性。

![process](process.png)

在控制台中，先输入`node`进入node环境，再输入`process.env`后即可打印出以下信息。`process.env`属性会返回包含用户环境的对象。通俗来讲，该属性可以返回项目运行环境的信息。

![process.env](processenv.png)

1. 可以修改此对象，但这些修改不会反映到 Node.js 进程之外，或者（除非明确地要求）反映到其他 Worker 线程。 
换句话说，这个示例不会起作用：`$ node -e 'process.env.foo = "bar"' && echo $foo`。
这个示例则会起作用：`process.env.foo = 'bar'; console.log(process.env.foo);`

2. 在 `process.env` 上为属性赋值会隐式地将值转换为字符串。 不推荐使用此行为。当值不是字符串、数字或布尔值时，Node.js 未来的版本可能会抛出错误。
```javascript
process.env.test = null;
console.log(process.env.test);
// => 'null'
process.env.test = undefined;
console.log(process.env.test);
// => 'undefined'
```

3. 使用delete可以从process.env中删除属性: `delete process.env.foo` 。
4. windows操作系统上，环境变量不区分大小写。
5. 除非在创建 Worker 实例时显式地指定，否则每个[Worker 线程](http://nodejs.cn/api/worker_threads.md#worker_threads_class_worker)都有自己的 process.env 副本（基于其父线程的 process.env，或者指定为 Worker 构造函数的 env 选项的任何内容）。 对于 process.env 的更改在 Worker 线程中是不可见的，并且只有主线程可以做出对操作系统或原生插件可见的更改。

## VueCLI中的模式和环境变量

### 模式
模式是 Vue CLI 项目中一个重要的概念。默认情况下，一个 Vue CLI 项目有三个模式：

1. development 模式：用于 `vue-cli-service serve`
2. production 模式：用于 `vue-cli-service build` 和 `vue-cli-service test:e2e`
3. test 模式：用于 `vue-cli-service test:unit`

可以通过传递 --mode 选项参数为命令行覆写默认的模式：`"dev-build": "vue-cli-service build --mode development"`

### 模式与环境变量

不同的模式会有不同的环境变量NODE_ENV：
在 development 模式下，环境变量为development， `NODE_ENV` 的值会被设置为 "development"
在 production 模式下，环境变量为production， `NODE_ENV` 的值会被设置为 "production"

在根目录下新建下列文件可以设置环境变量：

```
.env                # 在所有的环境中被载入
.env.local          # 在所有的环境中被载入，但会被 git 忽略
.env.[mode]         # 只在指定的模式中被载入
.env.[mode].local   # 只在指定的模式中被载入，但会被 git 忽略
```

可以通过为 .env 文件增加后缀，即设置文件`.env.模式名`来设置某个模式下特有的环境变量。
例如文件名为`.env.development`，在development环境下会载入该文件声明的变量。

> 环境加载属性时，特定环境文件高于一般环境文件的优先级
> 即为一个特定模式准备的环境文件 (例如 `.env.production` ) 将会比一般的环境文件 (例如 `.env`) 拥有更高的优先级。
> 
> 此外，Vue CLI 启动时已经存在的环境变量拥有最高优先级，并不会被 .env 文件覆写。

### 环境文件的内容书写

一个环境文件只包含环境变量的“键=值”对：

```env
FOO=bar
VUE_APP_SECRET=secret
```

## 变量命名：VUE_APP_*

[官网说明](https://cli.vuejs.org/zh/guide/mode-and-env.html#在客户端侧代码中使用环境变量)

<span style='color:red'>只有以`VUE_APP_`开头的变量会被`webpack.definePlugin`静态嵌入到客户端侧的包中，</span>访问时  `process.env.VUE_APP_SECRET`，添加变量直接以`VUE_APP_*`的形式就可。如`VUE_APP_BASE_URL = 'http://xxx.com/web/'`。因此我们添加变量的时候都以 `VUE_APP_*` 这样的形式。

除此之外,还有两个特殊的变量：
1. NODE_ENV : 会是 "development"、"production" 或 "test"中的一个。具体的值取决于应用运行的模式。可以自行修改。

```
NODE_ENV="可以进行修改"  
```
2. BASE_URL : 会和 `vue.config.js`中的publicPath选项相符，即你的应用会部署到的基础路径。

