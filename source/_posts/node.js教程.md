---
title: node.js教程
updated: 2022-09-21 16:23:17
date: 2022-09-21 16:23:17
tags: [node.js]
categories: [后端,node.js]
---

## 简介
Node.js是一个能够在**服务器端**运行JS的开放源代码的跨平台的**JS运行环境**。它基于谷歌的V8引擎，使用事件驱动、非阻塞和异步I/O模型等技术来提高性能，可优化应用程序的传输量和规模。在nodejs出现之前，JS通常作为客户端程序设计语言使用，以JS写出的程序常在用户的浏览器上运行。

+ 传统服务器一般都是多线程，每进来一个请求就会创建一个线程。
+ 但服务器返回响应之前需要先和数据库交互，涉及到磁盘相关的I/O处理，但I/O处理速度有瓶颈难以提升，所以可能会有很多线程在服务器进行等待，即I/O操作阻塞了线程的执行。此时这些线程虽然不干活，但也占用了服务器的内存。
![结构](jiegou.png)

+ node的服务器是单线程的。不过Node处理请求时是单线程，但是在后台拥有一个I/O线程池。
+ 对于nodejs，单线程是它的一大特点，即所有请求都只有一个线程，也不太会出现I/O阻塞。但单线程也使得它无法处理访问量太大的应用（那种一般用JAVA)；所以它的主要用处就是web服务器。比如某宝，客户端需要和用java开发的服务器交互，而java的优势在于处理请求很快，但渲染页面慢；js恰好有页面渲染快的优点。所以可以在上图的客户端和服务器中间来一层node服务器，这个服务器专门用来渲染页面。 

安装成功后，在终端输入```node```回车即可进入node环境（win需要配置环境变量）。输入```node 文件名.js```即可运行指定名字的js文件中的代码。

## 模块化
ES标准的缺陷：没有模块系统（ES6新增）、标准库较少、没有标准接口、缺乏管理系统。
模块化可以有多种形式，但至少应该提供能够将代码分割为多个源文件的机制；**CommonJS**的模块功能可以帮我们解决该问题。

> CommonJS规范
> 它的提出主要是为了弥补当前JS没有标准的缺陷。它对模块的定义十分简单：即**模块引用、模块定义和模块标识**。

模块分为两大类：
+ 核心模块：由node引擎提供的模块。核心模块的标识就是模块的名字。
+ 文件模块：由用户自己创建的模块。文件模块的标识就是文件的路径，常用相对路径。注意不要使用路径前的```.```或```..```。


### require()函数
在node中有一个全局对象```global```，它的作用和网页中的```window```类似；在全局中创建的变量都会作为```global```的属性保存，在全局中创建的函数都会作为```global```的方法保存。（注意在js中，声明变量时如果不用var，即用```a = 10```的方式声明，则该变量自动为全局变量。）
当node在执行模块中的代码时，它会首先在代码的最顶部添加如下代码（右 大括号在代码底部添加）：
```function(exports,require,module,__filename,__dirname){}```
实际上模块中的代码都是包装在这个函数中执行的，并且在函数执行时同时传递进了5个实参：

+ ```exports```：该对象用来将变量或函数暴露到外部
+ ```require```：函数，用来引入外部模块
+ ```module```：它代表的就是当前模块本身，**```exports```就是```module```的属性**。既可以使用```exports```导出，也可以使用```module.exports```导出。
+ ```__filename```：当前模块的完整路径（最后一级是该模块的名字）
+ ```__dirname```：当前模块所在文件夹的完整路径（最后一级是该模块所在文件夹的名字）


在node中，一个js文件就是一个模块。
在node中，每一个js文件中的js代码都是独立运行在一个函数中，**而不是全局作用域**。每个模块之间都是闭包。
> 所以虽然在被引入的模块里，有些东西看起来是定义在全局里的，也无法直接访问到。是因为相当于在外面又套了一层```function(){}```。
> 一个模块中的变量和函数在其他模块中无法访问。

**通过```require()```函数来引入外部模块。**
```require()```：可以传递一个文件的路径作为参数，node将会自动根据该路径引入外部模块。注意，如果使用相对路径，必须以```.```或```..```开头（不能随便省略！）。
使用```require()```引入模块后，该函数会返回一个对象，这个对象（如下面的```md```）代表的是引入的模块。
```var md = require("./temp.js")```

使用```require()```引入外部模块时使用的就是模块标识。我们可以通过模块标识来找到指定的模块。

### 暴露
可以通过```exports```来向外部暴露变量和方法，将需要暴露给外部的变量或方法设置为exports的属性即可，即```exports.xxx=...```。
```exports.x = "w我是temp.js中的x"```

关于exports 和 module.exports
```exports```只能使用```.```的方式向外暴露内部变量，如```exports.xxx = xxx```。而```module.exports```既可以通过```.```的方式，也可以直接赋值，如```module.exports.xxx = xxx```或```module.exports = {}```。

## npm和包
包实际上就是一个压缩文件，解压后还原为目录。CommonJS的包规范由**包结构**（用于组织包中的各种文件）和**包描述文件**（描述包的相关信息，以供外部读取分析）两个部分组成。 符合规范的目录应该包含如下文件：
——**package.json** 描述文件（只有这个是必须的，其他四个可选）。里面的字段有name、description、version等。可以通过```npm init```生成。
——bin 可执行二进制文件
——lib JS代码
——doc 文档
——test 单元测试
> .json文件中不支持写注释。

NPM（Node Package Manager）：帮助Node完成了第三方模块的发布、安装和依赖等。它不需要安装，安装完node之后自带npm。**通过npm下载的包都放到node_modules文件夹中，**通过npm下载的包直接通过包名引入即可。
node在使用模块名字来引入模块时，它会首先在当前目录的node_modules中寻找是否含有该模块。如果有则直接使用，如果没有则去上一级目录的node_modules找；没有再找上一级，直到找到为止。直到找到磁盘的根目录。如果依然没有，则报错。 
——```npm -v```：查看版本
——```npm search 包名```：搜索模块包
——```npm install 包名```：在**当前目录**安装包
——```npm install 包名 -g```：全局模式安装包。全局安装的包一般都是一些工具。 
——```npm remove 包名```：删除包
**——```npm install 包名 --save```：安装包并添加到依赖中（package.json--dependencies)。再配合```npm install```可直接下载当前项目所依赖的包并创建node_modules文件夹**。```npm remove 包名 --save```类似，也是从依赖中同时删除。
——```npm config set registry 地址```：设置镜像源
——```npm install 包名 -registry=地址```：从镜像源安装

> cnpm
> npm服务器在美国，在下载包的时候可能会遇到一些问题。可以直接搜索淘宝的cnpm查看教程。

## Buffer（缓冲区）
> JS已经有数组了，为什么还需要Buffer？
> JS的数组功能虽然也很强大，可以存储数字、字符串等各种数据类型，但在客户端和服务器的通信过程中，客户端给服务器发请求，服务器给客户端返回响应。如果服务器返回的是图片或者音视频文件，传统的数组array无法存储这些。
> 要存储这些**二进制文件**，就要用到buffer。

**buffer构造函数都是不推荐使用的，官网标注为“已废弃”。**
**buffer的大小一旦确定则不能修改。**buffer实际上是对底层内存的直接操作。
Buffer的结构和数组很像，操作的方法也和数组类似，它的元素是十六进制的两位数，范围是00 - ff。）不过数组中不能存储二进制的文件，而buffer就是专门用来存储二进制数据的，它只不过都是以十六进制的形式显示，由计算机的特性决定，因为二进制看起来太长了。

如果将一个字符串保存到buffer中：```var str = "Hello" var buf = Buffer.from(str)```，再对buf进行打印，结果为```<Buffer 48 65 6c 6c>```。 

> 为什么是到ff，也就是255？
> 因为在计算机中，一个0或一个1我们称之为是一位（1bit）。而8bit=1byte（字节）。
> 也就是buffer中的一个元素就占用内存的一个字节。

> 注意，只要数字在控制台或页面中输出一定是10进制。

需要注意的是，**buf.length**获取的是占用内存的大小，单位为byte。而**str.length**指的是字符串的长度。当字符串中的字符存在中文时既有明显的差别。
Buffer是nodejs的核心之一，使用buffer不需要引入模块，直接使用即可。

### buffer的相关方法
可以去Node.js中文网查看完整文档。

+ 创建指定大小的buffer：```var buf = Buffer.alloc(10) //创建一个10字节的buffer```。会在内存中分配一个长度为10的**连续**空间，可以通过索引来操作buf中的元素：```buf[2] = 0xaa```。 还有个```var buf = Buffer.allocUnSafe(10)```，也是创建一个指定大小为10字节的buffer，但是buffer中可能含有敏感数据。也就是alloc分配空间的同时清空数据，但allocUnSafe只分配，不清空数据。
  > Buffer大小一旦确定，则不能修改；不会像数组一样如果给一个索引超出长度的元素赋值就直接把数组长度变大的情况。Buffer实际上是对底层内存的直接操作。
  > alloc：allocate，分配。

+ 将buffer缓冲区中的数据转换为字符串：```buf.toString()```


## FS（文件系统）
文件系统简单来说就是通过node来操作系统中的文件。fs模块提供了一些标准文件访问API来打开、读取、写入文件以及与其交互。
> <span style="color:red">注意在写文件路径时，路径里的单斜杠"\\"可能会被当作转义字符，导致不能正确读取文件路径。把单斜杠变成双斜杠```\\```即可。也可以换成向左的斜杠```/```。</span> 

要使用fs模块，首先需要对其进行加载：```const fs = require("fs")```。fs是核心模块，直接引入而无需下载。（buffer模块甚至不需要引入。）

+ fs模块中所有的操作都有两种形式可以选择：<span style="color:red">同步和异步</span>。去官网看它的方法，发现都是一对对的，一个带Sync的（同步方法）和 一个不带的（异步方法）。
  + 同步文件系统会<span style="color:red">阻塞</span>程序的执行，也就是除非操作完毕，否则不会向下执行代码。
  + 异步文件系统<span style="color:red">不会阻塞</span>程序的执行，而是在操作完成时通过回调函数将结果返回。
  + 在方法的参数中，异步函数通常都有一个回调函数```callback```。因为异步函数需要通过回调函数在操作完成时返回结果，同步函数直接用```return```返回了。

### 同步和异步文件写入
#### 同步文件写入
手动操作的步骤：1.打开文件 2.向文件中写入内容 3.保存并关闭文件。
用fs，步骤如下：
1. 打开文件：```fs.openSync(path,flags[,mode])
- path：要打开文件的路径
- flags：打开文件要做的操作的类型。r - 只读的 w - 可写的
- mode：设置文件的操作权限，一般不传
- 返回值：该方法会返回一个文件的描述符作为结果，可以通过该描述符对文件进行各种操作。
2. 向文件中写入内容：```fs.writeSync(fd,string[,position[,encoding]])
- fd：文件的描述符，需要传递要写入的文件的描述符
- string：要写入的内容
- position：写入的起始位置，一般不传
- encoding：写入的编码，默认utf-8。一般不传
3. 保存并关闭文件：```fs.closeSync(fd)```
- fd：要关闭的文件的描述符

#### 异步文件写入
异步调用的方法，结果都是通过回调函数**的参数**```arguments```返回的。
异步文件写入的步骤如下：
1. 打开文件：```fs.open(path,flags[,mode],callback)```。比同步就多了个callback。
- callback：不可省略！ <span style="color:red">回调函数的两个参数：1.```err```：错误对象，没出错则为null 2.```fd```：文件的描述符。</span>

2. 异步写入文件：```fs.write(fd,string[,position[,encoding]],callback)```。<span style="color:red">在打开文件的回调函数中，如果没有出错，则对文件进行写入操作。</span>
<span style="color:red">回调函数的两个参数：1.```err```：错误对象，没出错则为null 2.```written```：指定传入的字符串被写入多少字节。3.```string```：写入的内容。</span>后面两个参数意义不大，写入内容在```fs.write```的参数中控制。 

3. 保存并关闭文件：```fs.close(fd)``` 
- callback：不可省略！ <span style="color:red">回调函数只有一个可能的异常参数。</span>

### 简单文件写入
第一种：```fs.writeFile(file,data[,options],callback)```
第二种：```fs.writeFileSync(file,data[,options])```

- file：要操作的文件的路径
- data：要写入的数据
- options：选项，可以对写入进行一些设置
- callback：当写入完成后执行的函数

用法如下：
```javascript
var fs = require("fs")
fs.writeFile("hello.txt", '这是通过writeFile写入的内容', {flag:"w"}, function(err){
    if(!err){
        console.log('写入成功！')
  }
})；
```

options的几个可选值如下：
![打开状态](dakaizhuangtai.png)
常用的有```r```, ```r+```, ```w```, ```a```。注意w是截断，a可以追加。r+如果输入的内容少于之前的，则从左往右替换，剩余的依然存在；如果多余之前的则全部替换之前的内容；是从头开始替换原文本而非插入。

### 流式文件写入 
以上几种写入都存在一个问题，就是它们是一次性的将一个内容写入一个文件里，因此在写入之前需要把要写入的东西一下子全部准备好。如果文件过大、内容过多，就会导致占用内存过大。
<span style="color:red">同步、异步和简单文件写入，都不适合大文件的写入。</span>性能较差，容易导致内存溢出。
流式文件写入就相当于往文件上加了个水管。只要水管还在，就可以一直输送内容。

1. 创建一个可写流：```fs.createWriteStream(path[,options])```。

- path：文件路径
- options：配置的参数

可以通过监听流的```open```.```close```事件来监听流的打开和关闭：```流名.on("open",callback)```，或把open换成close。但open事件只会触发一次，因为打开只有一次，而事件触发完之后函数依然在。这就说明，open事件是一个一次性的事件，没有必要用```on```，而是用```流名.once("open",callback)```。close同理。

- ```on(事件字符串，回调函数)```：可以为对象绑定一个事件
- ```once(事件字符串，回调函数)```：可以为对象绑定一个一次性的事件，该事件将会在触发一次以后自动失效。

> 上面的```on```是jquery里的写法，适用于绑定一个长期有效的事件。而```once```用来绑定一个一次性的事件。在这里使用这两个，运行效果是一样的。

2. 关闭流：```流名.end();```。
如果把流的传输想象成水缸A的水通过一个管子流向水缸B，这个方法相当于把管子从A拿走了，但另一头还在B，开始传输的东西会继续传输完才会完全关闭流。还有一个方法是```流名.close()```，这个方法就相当于把管子从B拿走，性能和计算机性能有关，性能好的话close()没执行就已经向文件写完了，所以会出现正常全部写入的情况。但性能不好的话，可能就无法传输完要传输的内容，流就已经关闭了。

用法如下：
```javascript
var fs = require("fs")
var ws = fs.createWriteStream("hihi.txt") // 这就创建了可写流
// 监听可写流的开启和关闭
ws.once("open", function () { console.log("流打开了"); });
ws.once("close", function () { console.log("流关闭了"); });

ws.write("通过可写流写入文件的内容")
ws.write("又写了")
ws.write("又又写了")

ws.close();
```

### 同步和异步文件读取
类似同步和异步文件的写入过程，先open，再```fs.read()```或```fs.readSync()```，再close。

### 简单文件读取
通过```fs.readFile(path[,options],callback)```和```fs.readFileSync(path[,options])```。

- path：要读取的文件的路径
- options：读取的选项（操作符、权限等），一般不写
- callback：回调函数，通过回调函数将读取到的内容返回```(err,data)```
  - err：错误对象
  - data：读取到的数据，会返回一个buffer。如果是文本文件，可以通过```data.toString()```来转换成正常的结果。如果是图片等其它类型的文件，就体现出buffer的好处了。

用法如下：
```javascript
var fs = require("fs")

fs.readFile("test.jpg",function(err,data){
    if(!err){
        // 1.打印读取到的内容：
        // console.log(data.toString());
        // 2.将data写入到文件中
        fs.writeFile("hihi.jpg",data,function(err){
            if(!err){ console.log("图片文件写入成功！");}
        })
    }
})
```

### 流式文件读取(初级方法)
流式文件读取也适用于一些比较大的文件，可以<span style="color:red">分多次将文件读取到内存中。</span>
如果要读取一个可读流的数据，必须要为可读流绑定一个data事件，data时间绑定完毕，它会自动开始读取数据；<span style="color:red">读取完了还会自动关闭，所以不需要专门去关闭可读流。</span>
关于读取到的数据的去向，可读流读取到的数据都是通过参数返回的，也就是下面示例的参数```data```（这个名字是我们自己起的）。而且参数是没有err的，因为这个事件一旦触发，就不可能再出错了。如果在打印data的地方同时打印```console.log(data.length)```，会发现结果是65536。

用法如下：
```javascript
var fs = require("fs")
// 创建一个可读流读取数据
var rs = fs.createReadStream("test.mp3") 
// 创建一个可写流接收数据
var ws = fs.createWriteStream("hihi.mp3") 

// 监听【可读】流和【可写】流的开启和关闭
rs.once("open", function () { console.log("可读流打开了"); });
rs.once("close", function () { 
    console.log("可读流关闭了"); 
    
    // 数据读取完毕，在这里关闭可写流：
    ws.end();
});
ws.once("open", function () { console.log("可写流打开了"); });
ws.once("close", function () { console.log("可写流关闭了"); });

rs.on("data",function(data){ 
    // console.log(data); 
    // 将读取到的数据写入到可写流
    ws.write(data)
    // 不可以在这里关闭可写流ws.end()!这就会导致只读了一条数据就把可写流给关了。
})
```

### 流式文件读取(简单方法)
方法```pipe()```：将可读流中的内容直接输出到可写流中。不需要再额外执行操作关闭可读流和可写流，```pipe()```会自动完成。
用法如下：
```javascript
var fs = require("fs")
// 创建一个可读流读取数据
var rs = fs.createReadStream("test.mp3") 
// 创建一个可写流接收数据
var ws = fs.createWriteStream("hihi.mp3") 

// 监听【可读】流和【可写】流的开启和关闭
rs.once("open", function () { console.log("可读流打开了"); });
rs.once("close", function () { console.log("可读流关闭了"); });
ws.once("open", function () { console.log("可写流打开了"); });
ws.once("close", function () { console.log("可写流关闭了"); });

// 简单方法：利用可读流的pipe()方法。在可读流和可写流中间架起一个管道，直接传输。
rs.pipe(ws);
```

### fs模块的其他方法
1. 验证路径是否存在：```fs.existsSync(path)```，返回boolean。可查看文件是否存在。异步方法已经弃用。
2. 获取文件信息：```fs.stat(path,callback)```和```fs.statSync(path)```，返回一个对象，保存了当前对象状态的相关信息。
3. 删除文件：```fs.unlink(path,callback)```和```fs.unlinkSync(path)```。
4. 列出文件：```fs.readdir(path,callback)```和```fs.readdirSync(path[,options])```。读取一个目录的目录结构。回调函数的参数files是一个字符串数组，每一个元素就是一个文件或文件夹的名字。
5. 截断文件，将文件修改为指定的大小：```fs.truncate(path,len,callback)```和```fs.truncateSync(path,len)```。这里的len是字节数。
6. 建立目录：```fs.mkdir(path[,mode],callback)```和```fs.mkdirSync(path[,mode])```。
7. 删除目录：```fs.rmdir(path,callback)```和```fs.rmdirSync(path)```。
8. 重命名文件和目录：```fs.rename(oldpath,callback)```和```fs.renameSync(path)```。
9. 监视文件更改写入：```fs.unlink(path,callback)```和```fs.unlinkSync(path)```。




