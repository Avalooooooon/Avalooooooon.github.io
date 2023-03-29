---
title: Webpack在vue.config.js中的配置
updated: 2023-03-10 11:19:20
date: 2023-03-10 11:19:20
tags: [Webpack]
categories: [前端,Webpack]
---

> configureWebpack与chainWebpack的作用相同，唯一的区别就是他们修改webpack配置方式不同：
> configureWebpack通过操作对象的形式来修改默认的webpack配置，该对象将会通过webpack-merge合并入最终的webpack配置；chainWebpack通过链式编程的形式来修改默认的webpack配置。
>
> 详细使用说明参考[vue-cli官网](https://cli.vuejs.org/zh/config/#configurewebpack)。

> <span style='color:red'>如果对一个loader或plugin修改的配置如果是 **一项** 的话推荐 chainWebpack、如果是 **多项** 的话用configureWebpack直接覆写。</span>

## configureWebpack配置项

类型多为对象。当这个配置项的值是对象时，会通过 webpack-merge 合并到最终的配置中。

```javascript
module.exports = {
  configureWebpack: {
      resolve: {  // 开发生产共同配置
          alias: { // 别名配置
              assets: '@/assets',
              components: '@/components',
              views: '@/views'
          },
          // vue解析时候查找文件的顺序
          extensions: [ '.mjs', '.js', '.jsx', '.vue', '.json', '.wasm' ],
          modules: [ // modules包的文件位置
            'node_modules',
            'C:\\Users\\shiyang.xue\\Desktop\\sems-pc\\node_modules',
          ],
      }
  }
}
```



## chainWebpack配置项

修改loader选项：

```javascript
module.exports = {
  chainWebpack: config => {
    config.module
    .rule('vue')
    .use('vue-loader')
    .tap(options => {
      // 修改他的选项
      return options
    })
  }
}
```

添加一个新的Loader：

```javascript
module.exports = {
  chainWebpack: config => {
    config.module
    .rule('graphql')
    .test('/\.graphql$/')
    .use(''graphql-tag/loader')
     .loader('graphql-tag/loader)
     .end()
    // 你还可以再添加一个loader
    .use('other-loader')
      .loader('other-loader')
      .end()
  }
}
```

替换一个规则里的Loader：

```javascript
module.exports = {
  chainWebpack: config => {
    const svgRule = config.module.rule('svg')
    // 清楚已有的所有loader。
    // 如果你不这样做，接下来的loader会附加在该规则现有的 loader 之后。
    svgRule.uses.clear()
    // 添加要替换的 loader 
    svgRule
      .use('vue-svg-loader')
        .loader('vue-svg-loader')
  }
}
```













