---
title: axios笔记
updated: 2022-11-01 15:44:59
date: 2022-11-01 15:44:59
tags: [JS]
categories: [前端,JS]
---

[官方文档]( https://github.com/axios/axios)
[一个开放接口](https://api.apiopen.top/api/sentences)

## Production
axios是前端最流行的 ajax 请求库，react/vue 官方都推荐使用 axios 发 ajax 请求。在浏览器中可以利用axios向服务端发送ajax请求来获取数据，在node环境可以利用axios向远端服务发送HTTP请求。也就是它可以在两个环境中运行。它有以下几个特点：

1. 基于 xhr + promise 的异步 ajax 请求库
2. 浏览器端/node 端都可以使用
3. 支持请求/响应拦截器
4. 支持请求取消
5. 请求/响应数据转换
6. 批量发送多个请求

axios的原理如下图：
![axios原理图](yuanli.png)

## 准备工作：使用 json-server 搭建REST API

[在线文档]( https://github.com/typicode/json-server)

json-server 是一个用来快速搭建REST API 的工具包，它可以快速帮我们搭建一个http服务。
> 需要搭建http服务是因为后期用axios的时候需要向服务端发送请求，因此我们需要一个这样的角色来与axios结合做实践。


1. 下载：```npm install -g json-server```，这里使用了全局安装
2. 目标根目录下创建数据库 json 文件：```db.json```
  ```json
  {
    "posts": [
        {
            "id": 1,
            "title": "json-server",
            "author": "typicode"
        },
        {
            "id": 2,
            "title": "json-server2",
            "author": "typicode"
        }
    ],
    "comments": [
        {
            "id": 1,
            "body": "some comment",
            "postId": 1
        }
    ],
    "profile": {
        "name": "typicode"
    }
  }
```
这里的```posts```：文章；```comments```：评论，```profile```：作者信息。
3. 启动服务：```json-server --watch db.json```。注意启动服务时命令行定位到的目录一定是db.json所位于的文件夹。这个命令可以理解成，现在要启动json-server，它启动的数据位于的文件是db.json，注意这个文件的路径要写对。
4. 成功启动后出现如图提示：
![成功启动服务](jsonserver.png)
可以看到列出了三个url，可以访问。分别是```posts```、```comments```和```profile```这三个资源。
5. 除了获取之外，可以在官网看到关于REST API的其他操作，部分如下：
![其他操作](elseserver.png)
第一行的含义是获取所有文章，第二行```/posts/id```意为获取指定文章；第三行```POST 路径```就是新增文件，请求路径是```/posts```，请求类型是```POST```，然后把数据传到请求体里就可以新增内容；还可以更新/删除内容。使用浏览器分别访问 http://localhost:3000/posts 和 http://localhost:3000/posts/1 进行测试。

至此已经实现了json-server服务的构建。有了它，就可以用axios向它发送请求然后获取结果了。

## axios 基本使用
查看[官方文档]( https://github.com/axios/axios)。
在给出的几种下载方式里，如果是在项目中使用axios，推荐使用npm或yarn的下载方式。现在由于是在学习，所以采用最后两种CDN的方式进行引入。
新建测试文件，引入axios并测试：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://cdn.jsdelivr.net/npm/axios@1.1.2/dist/axios.min.js"></script>
</head>
<body>
    <script> console.log(axios) </script>
</body>
</html>
```
> 又是cdn的网址（比如这里的https://cdn.jsdelivr.net/npm/axios@1.1.2/dist/axios.min.js ）可能会访问比较慢，为了提高访问速度，可以把这个资源换成国内服务器的。
> 访问https://www.bootcdn.cn 。在搜索框中输入axios，复制链接```<script src="https://cdn.bootcdn.net/ajax/libs/axios/1.1.3/axios.min.js"></script>```并在上面的html文件中进行替换。
> 两种链接的输出结果相同。

通过```axios()```函数发送AJAX请求。该函数接受一个对象作为参数，这个对象有几种属性：请求类型```method```、请求URL```url```等。

函数```axios()```的返回类型是一个promise，所以可以通过```then()```指定成功和失败时的回调。

### 示例：使用axios访问上面的json-server服务器并实现增删改查
> 注意这部分需要开启前文中已经配置好的json-server服务器！

在一个网页放四个按钮，尝试为这四个按钮添加可以实现相关功能的点击事件：点击不同按钮发送不同的ajax请求，包括GET、POST、PUT、DELETE。给上一节实现的json- server发请求，，url是```http://localhost:3000```，这里的端口号是3000。
![axios测试](axiostest.png)

1. 引入axios文件：```<script src="https://cdn.bootcdn.net/ajax/libs/axios/1.1.3/axios.min.js"></script>```
2. GET，即需要实现查看时的请求：
  ```javascript
  // 查看id为2的文章详情
  function testGet() {
    // 发送AJAX请求
    axios({
        // 请求类型
        method: 'GET',
        // URL
        url: 'http://localhost:3000/posts/2'
    }).then(response => {
        console.log(response)
    })
  }
```
  > 使用不同的url会有不同的输出结果：
  > axios.get('http://localhost:3000/posts') // 返回一个数组，数组里有两个对象
  > axios.get('http://localhost:3000/posts/1') // 返回一个对象
  > axios.get('http://localhost:3000/posts?id=1') // 返回一个数组，数组里有一个对象

直接点击GET按钮，控制台中可以看到打印的结果```response```。
> 如果控制台中的日志一闪就没，可以从右上角的小齿轮进入偏好设置，勾选控制台->“在浏览时保留日志”
> 网络中的日志一闪就没，可以在上一步的偏好设置中勾选网络->“保留日志”，也可以进入网络选项卡直接勾选“保留日志”。

打开网络（Network）选项卡，清空后点击GET按钮，可以看到请求已经成功发送。
![GET请求发送](get1.png)
点击名称为2的http请求，在“标头”（Headers）中选择“请求标头”（Request Headers），点击“查看源代码”（view source)，查看请求报文，看到请求类型“GET”和请求路径“/posts/2”：
![GET请求报文](get2.png)
点击“响应”（Response），查看返回结果，可以看到返回的确实是id为2的文章信息：
![GET请求响应](get3.png)
3. POST，即需要实现添加时的请求类型，<span style = "color:red">POST除了必须的请求类型和URL外还需要有请求体</span>：
  ```javascript
// 添加一篇新的文章
function testPost() {
    // 发送AJAX请求
    axios({
        // 请求类型
        method: 'POST',
        // URL
        url: 'http://localhost:3000/posts',
        // 请求体
        data: {
            title: "没课，好得很",
            author: "张三"
        }
    }).then(response => {
        console.log(response)
    })
}
```
直接点击POST按钮，控制台中可以看到打印的结果```response```。
打开网络（Network）选项卡，清空后点击POST按钮，可以看到请求已经成功发送。
![POST请求发送](post1.png)
点击状态码为201的http请求，在“标头”（Headers）中选择“请求标头”（Request Headers），点击“查看源代码”（view source)，查看请求报文，看到请求类型“POST”和请求路径“/posts”：
![POST请求报文](post2.png)
点击“载荷”（Payload），点击“查看源代码”（view source)，查看请求体（Request Payload），可以看到请求体是一个JSON格式的字符串：
![POST请求响应](post3.png)
传递给服务器的就是这个JSON格式的字符串，JSON-Server服务接收到这个东西后会对它进行保存。查看文件```db.json```，可以看到这个id为3的文章就已经添加好了。
4. PUT，即需要实现更新时的请求类型，打算把刚添加的那篇文章的作者从张三改成李四，<span style = "color:red">PUT除了必须的请求类型和URL外还需要有请求体和id</span>，也就是url的末尾需要添加```/id```：
  ```javascript
  // 更新数据
  function testPut() {
    // 发送AJAX请求
    axios({
        // 请求类型
        method: 'PUT',
        // URL
        url: 'http://localhost:3000/posts/3',
        // 请求体
        data: {
            title: "没课，好得很",
            author: "李四"
        }
    }).then(response => {
        console.log(response)
    })
  }
```
按照上面的步骤查看各内容；同时可以发现```db.json```也发生了变化，id3的作者已经成功变为李四。
5. DELETE，即需要实现删除时的请求类型，打算把之前添加的、在上一步中作者被改成了李四的那篇文章删掉，delete请求不需要请求体，直接把url一放就可以删除指定id的文章：
  ```javascript
// 删除数据
function testDelete() {
    // 发送AJAX请求
    axios({
        // 请求类型
        method: 'DELETE',
        // URL
        url: 'http://localhost:3000/posts/3',
    }).then(response => {
        console.log(response)
    })
}
```
按照上面的步骤查看各内容；同时可以发现```db.json```也发生了变化，id3已经成功删除。

完整的.html文件内的代码如下：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="https://cdn.bootcdn.net/ajax/libs/axios/1.1.3/axios.min.js"></script>
</head>
<body>
    <div>
        <button onclick="testGet()">GET请求</button>
        <button onclick="testPost()">POST请求</button>
        <button onclick="testPut()">PUT请求</button>
        <button onclick="testDelete()">DELETE请求</button>
    </div>
    <script>
        function testGet() {
            axios({
                method: 'GET',
                url: 'http://localhost:3000/posts/2'
            }).then(response => {
                console.log(response)
            })
        }
        function testPost() {
            axios({
                method: 'POST',
                url: 'http://localhost:3000/posts',
                data: {
                    title: "没课，好得很",
                    author: "张三"
                }
            }).then(response => {
                console.log(response)
            })
        }
        function testPut() {
            axios({
                method: 'PUT',
                url: 'http://localhost:3000/posts/3',
                data: {
                    title: "没课，好得很",
                    author: "李四"
                }
            }).then(response => {
                console.log(response)
            })
        }
        function testDelete() {
            axios({
                method: 'DELETE',
                url: 'http://localhost:3000/posts/3',
            }).then(response => {
                console.log(response)
            })
        }
    </script>
</body>
</html>
```

### axios其他方式发送请求
除了可以使用```axios()```函数发送请求，还可以使用```axios```对象身上的一些方法发送请求。下面给出两个使用示例，其它api的使用方法都类似，在对应的场景中可以选择合适的api使用。
1. ```axios.request(config)```: 等同于 axios(config)
它的使用方式和函数```axios()```一模一样，都是接收一个对象作为参数，这个对象里包括很多属性比如请求类型method、url等；它返回的也是一个promise对象。
把上一节中的GET按钮对应的函数改成如下代码进行测试：
  ```javascript
function testGet() {
    axios.request({
        method: 'GET',
        url: 'http://localhost:3000/comments'
    }).then(response => {
        console.log(response)
    })
}
```
> 可以省略配置项名称"method"和“url“，直接写即可。

可打开浏览器，查看控制台和network各个属性情况。
2. ```axios.post(url[, data, config])```: 发 post 请求
三个参数分别为url，请求体和config，后两个参数是可选的。
把上一节中的POST按钮对应的函数改成如下代码进行测试，要实现的功能是为id为2的文章添加评论，根据```db.json```中的格式编写请求体内容，这里省略属性名：
  ```javascript
function testPost() {
    axios.post(
        'http://localhost:3000/comments',
        {
            body: "json-server2的评论",
            "postId": 2
        }).then(response => {
            console.log(response)
        })
}
```

### axios响应结果的结构
> 响应报文包括四个部分：响应行、响应头、响应空行、响应体。

分别点击上一节中写好的两个按钮：GET和POST。在控制台console中可以看到输出结果（如果输出一闪而过，在右上角设置中勾选“保留日志”），点击response左边的展开按钮可以查看response详情（如果点击展开按钮无效，不要在live server运行.html文件，而是直接在文件管理器中打开文件）。

以点击GET按钮后的输出结果为例，如下图。
![响应结果](response.png)
有以下几种属性：

- config：配置对象，请求类型method、请求url、请求体等内容都在这一项中保存。
  ![响应：config](responseConfig.png)
- data：响应体。之所以是一个对象，是因为axios自动对服务器返回结果进行json解析并转化为对象，方便对结果进行处理。
  ![响应：data](responseData.png)
- headers：响应头。
  ![响应：headers](responseHeaders.png)
- request：原生的AJAX请求对象。axios的作用是发送ajax请求，而发送ajax请求需要用到底层的```XMLHttpRequest```；这个```request```所保存的就是当前axios在发送请求时所创建的那个AJAX请求对象，即```XMLHttpRequest```实例对象。
  ![响应：request](responseRequest.png)
- status：响应状态码。
- statusText：响应状态字符串。

### axios配置对象
[官方文档](https://github.com/axios/axios#request-config)

就是axios在调用时所接受的参数对象。这个配置对象并不单指```axios()```参数里的对象，还包括```request```、```get```、```POST```，它们当中都会用到这个参数对象；但凡是提到了```config```这个参数，指的其实都是配置对象。查看官方文档，可以看到这个配置对象中可以设置以下几种内容：

- url：指明要给谁发送请求
- method：设置请求类型，get、post、put什么的就在这设置
- baseURL：设定URL的基础结构，axios内部会自动将baseURL与url进行结合，形成最终的url结果。在这一项中填写协议、域名、端口，在url中只需填写后续路径即可。如baseURL：```http://localhost:3000```，url：```/comments```。
- transformRequest：它可以将请求的数据进行处理，再将处理后的结果发送给服务器。
- transformResponse：它可以将响应的数据进行处理，我们利用自定义的回调处理最后的结果。
> 以上两个参数分别对请求和响应的数据进行预处理，处理后再进行发送和响应的处理。

- headers：配置请求头信息。某些项目进行身份校验时就要求在头信息中加入一些特殊的标识，再检验请求是否满足条件，此时就可以利用headers控制请求头的信息。
- params：用来设定url参数，是一个对象。有时向服务端发送请求时需要传递url参数，如```a=100&b=200```，写起来不方便；axios允许我们在配置对象时添加一个属性```params```，可以在对象当中设置这些内容。比如想发对象，在url中写:```url:/posts```；想加参数，写一个```params:{ a:100,b:200 }```，最后这个params对象会变成一个参数字符串缀在url后面，再向服务端发送请求。
- paramsSerializer：配置参数序列化，用得较少。作用是对请求的参数做序列化，转变成一个字符串。比如请求的参数是```{ a:100,b:200 }```，默认情况下会把这个值转变成```/post?a=100&b=200```。也可能在服务端接口要求这样传：```/post/a/100/b/200```，或者```/post/a.100/b.200```，这种形式就需要对这个对象进行处理转换成正确格式的字符串，与服务器统一。
- data（对象形式）：设置请求体，有两种形式。如果是对象形式，如```data: { firstName: 'Fred' }```，axios会将其转化成json格式字符串进行传递。
- data（字符串形式）：设置请求体，有两种形式。如果是字符串形式，如```data: 'Country=Brasil&City=Belo Horizonte'```，axios会将其直接传递。其实就是表单传送方式，键名=键值+```&```符分割。
> 这两种方式都会经常用到。如果项目中要求请求体为JSON，就用第一种；要求请求体为url的参数传递形式的话就用第二种。

- timeout：发送请求的最大允许超时时间，单位ms。如果超过这个时间请求就会被取消。
- withCredentials：设置跨域请求时cookie的携带。false是不携带，true则意味着跨域请求时会把cookie携带过去。
- adapter：设置请求的适配器。有两种，分别是发送AJAX的，和在nodejs中发送http的。两个运行环境。
- auth：设置请求的基础验证。用户名密码。
- responseType：设置响应体的结果格式。默认值为JSON，即服务器默认的返回结果是JSON格式的，结果到达后会自动对其做一个转换。
- responseEncoding：设置响应结果的编码。默认值为```'utf8'```。
- xsrfCookieName：设置跨站请求的标识：对cookie的名字进行设置。
- xsrfHeaderName：设置跨站请求的标识：对头信息进行设置。
> 这两个是安全设置，保证请求是来自自己的客户端而不是一些其他的未知客户端。
  > 为什么能实现保护作用？需要结合服务器进行说明。服务器在返回结果时会返回一个唯一标识，下次发请求时需要把这个唯一标识再传递过去，服务器认出它、检测没有问题之后才会做响应。
  > 有些网站页面里会加入一些链接，向我们的服务器发送请求。如果不做唯一标识去检验它，可能这个页面发送的请求就会直接对我们的结果造成影响；加了这个唯一参数后，我们的客户端可以把它加上，而其他客户端没有，就可以有效避免跨站攻击。

- onUploadProgress：上传时的回调。
- onDownloadProgress：下载时的回调。
- maxContentLength：设置HTTP响应体的最大尺寸，单位为字节。
- maxBodyLength：设置HTTP请求体的最大尺寸，单位为字节。
- validateStatus：设置如何认定响应结果是成功的。一般不改。默认值为```status >= 200 && status < 300```，即响应代码>200并且<300。
- maxRedirects：最大跳转次数。当我们向一个服务发送请求时它做了跳转，做了跳转后还要不要继续向前进行请求。一般在nodejs用，前端的AJAX用不到。
- socketPath：设置socket文件的位置。这个文件的作用是向docker的守护进程发送请求，即数据转发。和proxy有先后优先级的关系，如果同时设置了socket和文件代理proxy，优先使用socket
- httpAgent：设置客户端信息。如```keep-alive```（是否保持连接）。少用。
- proxy：设置代理。只能在nodejs用。
> 做爬虫时，如果只用一个ip向目标服务器抓取数据，很可能被别人进到自己的ip。这时就可以借助很多中间代理，不断切换，发送请求。投票等任务同理。

- cancelToken：对AJAX请求做取消的设置。
- decompress：对响应结果是否进行解压。只能在nodejs用。

### axios默认配置
[官方文档：Config Defaults](https://github.com/axios/axios#config-defaults)
它可以把一些重复性的设置配置在默认设置中，从而简化代码。
思考本章第一小节各个按钮的实现。下面是GET按钮的实现：
```javascript
function testGet() {
  axios({
      method: 'GET',
      url: 'http://localhost:3000/posts/2'
  }).then(response => {
      console.log(response)
  })
}
```
在每个按钮的实现中都需要写请求类型```method: 'xxx'```，而且每一次```url```都得写的很长。可以在方法外面添加默认配置：
```javascript
axios.defaults.method = 'GET'; // 设置默认的请求类型为GET
axios.defaults.baseURL = 'http://localhost:3000' // 设置基础URL
```
在后续的使用过程中，默认配置中写的内容无需重新配置。GET按钮的实现可以修改为下面这样，效果相同：
```javascript
function testGet() {
    axios.request({
        url: '/posts'
    }).then(response => {
        console.log(response)
    })
}
```
查看官方文档，可以看到其他可以设置的值。下面测试默认```params```，配置后能否在network里看到请求添加了指定后缀params。设置语句为```axios.defaults.params = { id: 100 };```，可以看到成功配置。
![配置默认params](defaultParams.png)

### axios.create(config)
<span style = "color:red">创建一个拥有通用配置的axios实例:```axios.create([config])```。</span> ```axios.create()```函数接受一个配置对象作为参数，里面可以写```baseurl```、```timeout```等等参数。

即根据指定配置创建一个新的 axios，也就是每个新 axios 都有自己的配置。新 axios 只是没有取消请求和批量发请求的方法，其它所有语法都是一致的。

> 创建实例对象发送请求的优势：
> 当项目的接口数据服务不只来自于一个单一的服务器，有着不同的协议、域名、端口，此时用默认配置只能省一个事；这时候设置两个不一样名字的axios示例就很合适。
> 接下来向不同的接口发请求时借用不同的实例对象即可。

### axios创建实例对象发送AJAX请求
具体创建如下（只展示了.html文件中的script部分代码）：
```javascript
// 创建实例对象
const duanzi = axios.create({
    baseURL: 'https://api.apiopen.top',
    timeout: 2000
})
// duanzi 与 axios 对象的功能几近一样
duanzi({
    url: '/api/sentences',
}).then(response => {
    console.log(response)
})
```
通过```create()```创建出来的对象与axios本身的功能几近一致，这个新创建出来的对象也是当一个函数使用的。示例如下：
```javascript
// duanzi 与 axios 对象的功能几近一样
duanzi({
    url: '/api/sentences',
}).then(response => {
    console.log(response)
})
```

除此之外，当然也可以利用封装好的方法发送请求。注意下面打印的结果是我们新创建的接口```duanzi```为我们返回的<span style = "color:red">JSON数据转换后的js对象</span>：
```javascript
duanzi.get('/api/sentences').then(response => {
    console.log(response.data)
})
```

### 拦截器（Interceptors）
[官方文档：Interceptors](https://github.com/axios/axios#interceptors)
拦截器实际上就是一些函数，即 **满足条件放行** 。它分为两大类：请求拦截器 和 响应拦截器。

+ 请求拦截器：发送请求前可以借助一些函数，对请求参数和内容做一些处理和检测，都没问题就去发送请求。如果存在问题，可以直接取消请求。
+ 响应拦截器：服务器返回结果后，我们可以通过自己指定的回调对结果进行处理。响应拦截器可以在我们处理结果之前先对结果预处理，（比如失败则做一个失败结果的提醒、格式化处理数据结果。。。），没问题后再交由我们自己处理。有问题则在响应拦截器中处理掉，我们不需要去处理失败结果。

下面给出示例。复制粘贴官网上的代码放入```<script>```标签。可以看出，函数```axios.interceptors.request.use()```传入了两个函数作为参数，还有成功、失败两种情况的处理，可以知道拦截器的原理也是和promise相关的。

请求拦截器中的参数```config```：配置对象。也就是在请求拦截器中可以调整请求参数，比如可以利用语句```config.params = {a:100}```修改config中的参数。
响应结果中的```response```：响应结果```response```是axios自动生成的默认的响应结果，里面除了真正的响应结果```data```之外还有很多响应状态码之类的东西。多数情况下不需要获取那些多余的东西，用啥就返回啥，比如可以直接返回```response.data```，这样就只有响应体而没有响应行响应头这些东西。
```javascript
// 设置请求拦截器
axios.interceptors.request.use(function (config) {
    console.log('请求拦截器 成功')
    return config;
}, function (error) {
    console.log('请求拦截器 失败')
    return Promise.reject(error);
});
//设置响应拦截器
axios.interceptors.response.use(function (response) {
    console.log('响应拦截器 成功')
    return response;
}, function (error) {
    console.log('响应拦截器 失败')
    return Promise.reject(error);
});
// 发送请求
axios({
    method: 'GET',
    url: 'http://localhost:3000/posts'
}).then(response => {
    console.log(response)
})
```
这段代码的执行顺序应该是先走请求拦截器，成功后发送请求，然后交给响应拦截器初步处理返回结果，最后交给我们自己设定的回调进行处理。可以看到打印的结果如下图：
![拦截器-处理成功](lanjieqi1.png)
在代码中进行以下修改，让接受成功的请求拦截器throw一个异常并在我们自己的回调函数逻辑中添加catch：
```javascript
axios.interceptors.request.use(function (config) {
    console.log('请求拦截器 成功')
    throw '参数出了问题'
}, function (error) {
    console.log('请求拦截器 失败')
    return Promise.reject(error);
});
axios.interceptors.response.use(function (response) {
    console.log('响应拦截器 成功')
    return response;
}, function (error) {
    console.log('响应拦截器 失败')
    return Promise.reject(error);
});
axios({
    method: 'GET',
    url: 'http://localhost:3000/posts'
}).then(response => {
    console.log(response)
}).catch(reason => {
    console.log('自定义失败回调')
})
```
读到```throw '参数出了问题'```时会返回一个失败的 **promise** ，所以在执行后续回调的时候就走不了处理成功的回调了，只能走负责处理失败结果的回调。则执行顺序变为：请求拦截器成功并抛出异常，返回失败promise->响应拦截器收到失败promise，进行异常处理，再返回失败promise->我们自己的回调函数收到失败promise并进行处理。控制台结果如图。
![拦截器-处理失败](lanjieqi2.png)

> 如果有多个请求，请求拦截器和响应拦截器的执行顺序：请求从外边到里边，最后设置的在最外边（倒序），响应就是里到外（正序）。
> <span style = "color:red">请求拦截器进入的是堆栈，响应拦截器进入的是队列。</span>

## axios源码
### 目录结构

├──  /dist/ # 项目输出目录，存放打包后的文件
│    ├──  axios.js 未压缩的
│    └── axios.min.js 压缩后的 一般项目引入的是这个
├──  /lib/ # 项目源码目录 
│    ├──   /adapters/ # 定义请求的适配器 xhr、http 
│    │    ├── http.js # 实现 http 适配器(包装 http 包) ，用来在nodejs中向远端服务器发送http请求
│    │    └──xhr.js # 实现 xhr 适配器(包装 xhr 对象) ，用来在前端中发送AJAX请求
│    ├──   /cancel/ # 定义取消功能 
│    │    ├── Cancel.js # Cancel构造函数，用来创建取消时的错误对象
│    │    ├── CanceToken.js # CancelToken构造函数，用来创建取消请求对象
│    │    ├── isCance.js # 检测参数是否为取消对象
│    ├──   /core/ # 一些核心功能 
│    │    ├── Axios.js # axios 的核心主类。Axios构造函数文件
│    │    ├── buildFullPath.js # 构建完整URL的函数文件
│    │    ├── dispatchRequest.js # 用来 **调用** http 请求适配器方法发送请求的函数 
│    │    ├── InterceptorManager.js # 拦截器的管理器 
│    │    └── settle.js # 根据 http 响应状态，改变 Promise 的状态 
│    ├──   /helpers/ # 一些辅助方法 
│    ├──   axios.js # axios的入口文件。对外暴露接口。
│    ├──   defaults.js # axios 的默认配置文件（配置对象）
│    ├──   utils.js # 公用工具 
├─ package.json # 项目信息 
├─ index.d.ts # 配置 TypeScript 的声明文件 
└─ index.js # 是 **整个包** 的入口文件

### axios创建过程
> axios 与 Axios 的关系:
> 
> 1. 从语法上来说: axios 不是 Axios 的实例
> 2. 从功能上来说: axios 是 Axios 的实例
> 3. axios 是 Axios.prototype.request 函数 bind()返回的函数
> 4. axios 作为对象有 Axios 原型对象上的所有方法, 有 Axios 对象上所有属性
> 

后面再更新；


## axios 常用语法
1. ```axios(config)```: 通用/最本质的发任意类型请求的方式
2. ```axios(url[, config])```: 可以只指定 url 发 get 请求
3. ```axios.request(config)```: 等同于 axios(config)
4. ```axios.get(url[, config])```: 发 get 请求
5. ```axios.delete(url[, config])```: 发 delete 请求
6. ```axios.post(url[, data, config])```: 发 post 请求
7. ```axios.put(url[, data, config])```: 发 put 请求
8. ```axios.defaults.xxx```: 请求的默认全局配置
9. ```axios.interceptors.request.use()```: 添加请求拦截器
10. ```axios.interceptors.response.use()```: 添加响应拦截器
11. ```axios.create([config])```: 创建一个新的 axios(它没有下面的功能)
12. ```axios.Cancel()```: 用于创建取消请求的错误对象
13. ```axios.CancelToken()```: 用于创建取消请求的 token 对象
14. ```axios.isCancel()```: 是否是一个取消请求的错误
15. ```axios.all(promises)```: 用于批量执行多个异步请求
16. ```axios.spread()```: 用来指定接收所有成功数据的回调函数的方法