---
title: Vue教程
updated: 2022-01-08 22:33:40
date: 2022-01-08 22:33:40
tags:
categories:
---

参考：[Vue中文官网](https://cn.vuejs.org)

安装环境：Windows10
## 简介
Vue是一套用于构建用户界面的渐进式JS框架，渐进指Vue可以根据应用的复杂程度自底向上逐层的应用，对于简单应用它只需要一个清凉小巧的核心库，对于复杂应用则可以引入各式各样的Vue插件。
它有以下特点：
+ 采用组件化模式，提高代码复用率且更好维护。
![组件化示意](zujianhua.png)
+ 声明式编码，让编码人员直接操作dom，提高开发效率。
![声明式编码](shengmingshi.png)
+ 使用虚拟dom+优秀的diff算法，尽量复用dom节点。
![虚拟dom](xunidom.png)
+ 
## 安装
环境：Windows10
### 安装node和npm环境

### 安装脚手架vue-cli

```bash
npm install webpack -g
npm install --global vue-cli
vue -V
```
其中webpack是一个包管理工具，也是vue-cli的构建工具。上述命令可以完成webpack的全局安装。如果用nvs工具，可以在C:\Program Files\nodejs\node_global中查看全局安装的vue-cli。
![vue-cli在文件夹中的显示](vuecli.png)

### 构建工程文件
cd到工具区。在工作区中```vue init webpack testproj```以生成webpack脚手架。回车后可能会出现一些提示问题：
+ 项目名称（注意名称中不要出现大写字母，否则会报错）
+ 项目描述（可写可不写，看个人需要）
+ 作者（可写可不写，看个人需要）
+ vue编译，这个选默认即可，运行加编译Runtime + Compiler
+ 是否安装vue-router是否安装vue路由工具
+ 是否使用代码管理工具ESLint管理你的代码
+ 后面几个是测试的工具，需要自己自行了解
+  可以选择在```testproj```工程新建好后系统自动进行初始化，安装package.json中描述的依赖。
初始化完成后，cd进入项目文件夹。```npm run dev```运行项目。运行完成后打开浏览器，输入http://localhost:8080/ ，看到Welcome to Your Vue.js APP页面就可以说明项目脚手架已经初始化完成。
> 若出现报错```'webpack-dev-server' 不是内部或外部命令，也不是可运行的程序或批处理文件```，运行```npm install webpack-dev-server --save-dev```即可解决。

