---
title: vue.config.js前端代理
tags:
  - Vue
categories:
  - 前端
  - Vue
updated: 2023-03-10 11:42:26
date: 2023-03-10 11:42:26
---




> vue项目根目录下找到vue.config.js文件（如果没有该文件则自己创建），在proxy中设置跨域。解决的问题是前后端分离而产生的本地开发跨域问题。
>
> 注意，该文件修改后必须重新启动服务才能生效！

## 同源和跨域

同源策略：两个页面拥有相同的协议（protocol）、域名/主机（host）、端口号（port）
是浏览器方的策略。

也正是因为它是浏览器方的策略，所以只有浏览器和服务器之间会存在跨域问题，两个服务器之间不存在跨域，可以随意访问。所以我们要配置一个代理服务器来调用服务器的接口，比如可以利用node层代理，将域名发给本地服务器，再由本地服务器请求目的服务器。

如果你的前端应用和后端API服务器没有运行在同一个主机（host）上，你需要在开发环境下将API请求代理到API服务器，此时可以通过 vue.config.js 中的 devServer.proxy 选项来配置。

当我们请求一个接口的时候，控制台报错出现如`Access-Control-Allow-Origin`字眼的时候就说明请求跨域了。



## 配置项devServer的作用

> 详细情况可以去[官方文档](https://cli.vuejs.org/zh/config/#devserver)查询。

devServer里面proxy是关于代理的配置，该属性为对象的形式，对象中每一个属性就是一个代理的规则匹配。<span style='color:red'>属性的名称是需要被代理的请求路径前缀，一般为了辨别都会设置前缀为/api，值为对应的代理匹配规则</span>，对应如下：

+   target：表示的是代理到的目标地址
+   pathRewrite：默认情况下，我们的 /api 也会被写入到URL中，如果希望删除，可以使用pathRewrite。如果你的接口中本身就带有 /api 可以不写 pathRewrite。
+   secure：默认情况下不接收转发到https的服务器上，如果希望支持，可以设置为false
+   changeOrigin：它表示是否更新代理后请求的 headers 中host地址

如果前端应用和后端API服务器没有运行在同一个主机上，就需要在开发环境下将API请求代理到API服务器。通过vue.config.js中的devServer.proxy选项来配置。
devServer.proxy 可以是一个指向开发环境 API 服务器的字符串：

```javascript
module.exports = {
  devServer: {
    // 以下几个配置不常用
    index: '/login.html',   //默认打开文件
    host: 'localhost',      //默认打开域名
    hotOnly: false,         //热更
      
    // 常用配置
    port: '9080', //默认打开的端口号
    open: true, // 是否自动打开网页
    proxy: {
    '/api': {
      target: 'http://192.168.2.108:9080/test',//代理地址，这里设置的地址会代替axios中设置的baseURL
      secure: false, //如果是https接口需要进行此配置 
      changeOrigin: true,// 如果接口跨域，需要进行这个参数配置
      pathRewrite: {  //pathRewrite方法重写url
        '^/api': '/aaa' //如果没有pathRewrite属性 调用的接口就是http://cloud/api/xxx/xxxx 如果像这里配置了pathRewrite属性 调用的接口就是 http://cloud/aaa/xxx/xxxx
      }
    }
  }
}
```

这会告诉开发服务器将任何未知请求 (没有匹配到静态文件的请求) 代理到`http://192.168.2.108:9080/test`。

## 在vue中通过devServer.proxy实现跨域

proxy实质就是一个代理服务器。在vue中使用proxy进行跨域的原理是：将域名发送给本地的服务器（启动vue项目的服务，localhost8080），再由本地的服务器去请求真正的服务器。这样就能理解 proxy 工作原理实质上是利用http-proxy-middleware这个http代理中间件，实现请求转发给其他服务器。

举个例子：
在开发阶段，本地地址为`http://localhost:3000`，该浏览器发送一个前缀带有`/api`标识的请求到服务端获取数据，但响应这个请求的服务器只是将请求转发到另一台服务器中。说的再通俗一点 就是 A(浏览器)想认识(发请求) C(服务器)，但由于碍于面子(同源政策) 只能通过B(proxy)来介绍，也就是 A 发送请求给 B 再由 B 转发请求给 C。

在<span style='color:red'>开发阶段，devServer 会启动一个 **本地开发服务器**，所以我们的应用在开发阶段是独立运行在 localhost的一个端口上，而后端服务又是运行在另外一个地址上。</span>所以在开发阶段中，由于浏览器同源策略的原因，本地访问后端就会出现跨域请求的问题。

通过设置 proxy 实现代理请求后，相当于浏览器与服务端中添加一个代理者。<span style='color:red'>当本地发送请求的时候，代理服务器响应该请求，并将请求转发到目标服务器，目标服务器响应数据后再将数据返回给代理服务器，最终再由代理服务器将数据响应给本地。</span>

示例如下。<span style='color:red'>注意配置中重写了`/api`为空的字符串，因为真正请求的地址是没有带/api的！</span>假如请求的真正地址为：`http://192.168.1.1:8070/shop/car`,但我们在浏览器上会看到是这样的： `http://192.168.1.1:8070/api/shop/car` ,多了个/api，但并不影响我们请求数据。浏览器控制台虽然看到的还是带有/api的，但是实际发给后端的地址是没有包含/api的。



```javascript
devServer: {
  proxy:{ // 配置跨域
    '/api':{
      target: 'http://192.168.1.1:8070',  //代理的服务器，也就是api要访问的服务器。这里后台的地址模拟的，应该填写真实的请求url
      ws:true,    //是否启用websockets
      changOrigin: true, //true，将主机头的来源更改为目标URL，也就是是否允许跨域。
                         //开启代理：在本地会创建一个虚拟服务端，然后发送请求的数据，并同时接收请求的数据，这样服务端和服务端进行数据的交互就不会有跨域问题
      pathRewrite: {    //重写代理路径
         '^/api': ''
        }
    },
}
```

> 关于proxy配置项中的/api和pathRewrite：
> 假设本地路径是`http://127.0.0.1:8080`。proxy对象的’/api’属性相当于是一个标识，告知node：路径为/api时，我需要代理帮我跨域，地址是`https://lst.com:3000`，此时请求地址为`https://lst.com:3000/api/xx/xxxx`，但我真正要请求的地址是`https://lst.com:3000/xx/xxxx`
> 这时候pathRewrite属性就很重要了，’^/api’:’ ’ 利用正则匹配/api，替换成空字符串，这样请求地址就变成了我们想要的地址。


## 配置多个代理地址

有时候系统有多个不同的代理地址，加一份代理即可！在vue.config.js中，通过配置属性devServer和devServer.proxy来配置多个代理地址。

```javascript
devServer:{
  proxy:{
    '/api':{
      target: 'http://192.168.1.1:8070',   //这里写真实的请求url
      ws:true,    //是否启用websockets
      changOrigin: true, 
      pathRewrite: { //重写代理路径
         '^/api': ''
        }
    },
    '/test':{
      target: 'http://192.168.1.1:8092',   //这里写真实的请求url
      ws:true,
      changOrigin: true, //允许跨域 
      pathRewrite: {    
         '^/test': ''
        }
    }
  }
},
```







