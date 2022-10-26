---
title: Promise教程
updated: 2022-10-25 17:02:31
date: 2022-10-25 17:02:31
tags: [JS]
categories: [前端,JS]
---

## Production
### promise是什么
ES6引入的异步编程的新解决方案（旧方案是单纯使用回调函数）。语法上看，<span style="color:red">**promise是一个构造函数**</span>；功能上，promise是一个对象，通常用来封装一个异步任务，提供承诺结果（其成功/失败）。
> 常见的异步编程：
> fs文件操作：```require('fs').readFile('./index.html',(err,data)=>{})```
> mysql/mangodb等数据库操作
> AJAX(jquery为例)：```$.get('/server',(data)=>{})```
> 定时器：```setTimeout(()=>{},2000)```

通俗讲，Promise是一个对未来事情的许诺、承诺，承诺不一定能完成，但是无论是否能完成都会有一个结果。Promise 用来预定一个不一定能完成的任务，要么成功，要么失败。

### promise的状态「PromiseState」
> <span style = "color:red">状态是promise实例对象的一个属性：```PromiseState```</span>。

Promise的状态如下，作为promise参数函数的参数时，它们自己是函数类型的数据：

+ pending：未决定的（正在做。。。）
+ resolved/fullfilled：成功（完成这个承诺）。异步任务成功时调用。
+ rejected：失败（承诺没完成，失败了）。异步任务失败时调用。

使promise状态改变的方法和结果如下，<span style = "color:red">只有这两种可能，一个promise对象的状态只能改变一次</span>：

+ 当前是pending ，变为 resolved：
    a. 通过 ```resolve(value)```
+ 当前是pending ，变为 rejected
    a. 通过```reject(reason)```
    b. 通过抛出异常，可以使用```throw```关键字，如```throw "出现错误"```

    > 状态改变只有这 2 种, 且一个 promise 对象只能改变一次。无论变为成功还是失败, 都会有一个结果数据 。成功的结果数据一般称为 value, 失败的结果数据一般称为 reason。

### promise对象的值「PromiseResult」
> <span style = "color:red">是promise实例对象的另一个属性：```PromiseResult```</span>。

保存的是异步任务成功或失败的结果。有两个函数可以修改这个值：

+ resolve
+ reject
就是```new Promise((resolve, reject) => {}```中的```resolv```和```reject```。

![promise的基本流程](liucheng.png)

### 特点和优缺点
优势：
1. <span style="color:red">支持链式调用，可以解决**回调地狱**问题。</span>
  ![回调地狱](huidiaodiyu.png)
  > 回调地狱：回调函数嵌套调用，外部回调函数异步执行的结果是套在内部的回调函数执行的条件。即一层一层的嵌套，上一个方法拿到数据后在执行下一个。
  > 回调地狱的缺点：不便于阅读 + 不便于异常处理
  

    不过promise只是简单的改变格式，并没有彻底解决上面的问题。真正解决要利用```promise```再加上```await```和```async```关键字实现异步传同步。
2. 指定回调函数的方式更加灵活
    + 之前是在启动异步任务之前就必须把回调函数绑定好
    + promise可以：启动异步任务=>返回promise对象=>给promise对象绑定回调函数（甚至可以在异步任务结束后指定多个）

特点:
1. 对象的状态不受外界影响。Promise对象代表一个异步操作，有三种状态：Pending（进行中）、Resolved（已完成，又称Fulfilled）和Rejected（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。
2. 一旦状态改变，就不会再变，任何时候都可以得到这个结果。Promise对象的状态改变，只有两种可能：从Pending变为Resolved和从Pending变为Rejected。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果。就算改变已经发生了，你再对Promise对象添加回调函数，也会立即得到这个结果。

缺点:
1. 无法取消Promise，一旦新建它就会立即执行，无法中途取消。和一般的对象不一样，无需调用。
2. 如果不设置回调函数，Promise内部抛出的错误，不会反应到外部。
3. 当处于Pending状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）

### 它的小伙伴：then()
Promise实例生成以后，可以用 ```then()```方法分别指定resolved状态和rejected状态的回调函数。
```javascript
promise.then(function(value) {  // (value)=>{}
    // 当promise状态返回为resolve 时会执行的回调函数
    }, function(error) {  // (reason)=>{}
    // 当promise状态返回为rejected 时会执行的回调函数
});
```
```then()```方法可以接受两个回调函数作为参数。第一个回调函数是Promise对象的状态变为resolved时调用，第二个回调函数是Promise对象的状态变为rejected时调用。
第二个函数是可选的，不一定要提供。这两个函数都接受Promise对象传出的值作为参数。

如果形参只有一个，使用箭头函数时可以简写，不要小括号，即可以将```(value)=>{}```写作```value =>{}```。

### 基本使用方法示例
1. 使用 promise 封装基于定时器的异步

```javascript
function doDelay(time) {
    // 1. 创建 promise 对象(pending 状态), 指定执行器函数
    return new Promise((resolve, reject) => {
        // 2. 在执行器函数中启动异步任务
        console.log('启动异步任务')
        setTimeout(() => {
            console.log('延迟任务开始执行...')
            const time = Date.now() // 假设: 时间为奇数代表成功, 为偶数代表失败
            if (time % 2 === 1) { // 成功了
                // 3. 1. 如果成功了, 调用 resolve()并传入成功的 value
                resolve('成功的数据 ' + time)
            } else { // 失败了
                // 3.2. 如果失败了, 调用 reject()并传入失败的 reason
                reject('失败的数据 ' + time)
            }
        }, time)
    })
}
const promise = doDelay(2000)
promise.then(// promise指定成功或失败的回调函数来获取成功的value或失败的reason
    (value) => {// 成功的回调函数 onResolved, 得到成功的value
        console.log('成功的value: ', value)
    },
    (reason) => { // 失败的回调函数 onRejected, 得到失败的reason
        console.log('失败的reason: ', reason)
    },
)
```
2. fs模块使用promise
```javascript
const fs = require('fs');

//回调函数 形式----------------------------------------------------
fs.readFile('./resource/content.txt', (err, data) => {
    // 如果出错 则抛出错误
    if (err) throw err;
    //输出文件内容
    console.log(data.toString());
});

//Promise 形式-----------------------------------------------------------
/**
 * 封装一个函数 mineReadFile 读取文件内容
 * 参数:  path  文件路径
 * 返回:  promise 对象
 */
function mineReadFile(path) {
    return new Promise((resolve, reject) => {
        //读取文件
        require('fs').readFile(path, (err, data) => {
            //判断
            if (err) reject(err);
            //成功
            resolve(data);
        });
    });
}

mineReadFile('./resource/content.txt')
    .then(value => {
        //输出文件内容
        console.log(value.toString());
    }, reason => {
        console.log(reason);
    });
```

### 异常穿透
可以在每个```then()```的第二个回调函数中进行err处理；
也可以利用异常穿透特性，到最后用```catch```去承接统一处理。
两者一起用时，只有前者会生效（因为err已经将其处理，就不会再往下穿透）而走不到后面的```catch```。
在每个```.then()```中可以将数据再次传出给下一个```then()```。
```javascript
mineReadFile('./11.txt').then(result=>{
  console.log(result.toString())
  return result
},err=>console.log(err))
.then(data=>console.log(data,"2222222"))
.catch(err=>console.log("这是catch的"))
```

### 使用util.promisify()方法进行风格转化
想使用promise方法时，不用每个函数都要去手动封装，而可以借助```util.promisify()```方法将原来的回调函数风格的方法转变成promise风格。也就是把这个普通异步函数的方法给它，它帮我们转化成promise风格的。
传入一个遵循常见的错误优先的回调风格的函数（即以```(err,value)=...>```回调作为最后一个参数），并返回一个**返回promise的版本**。
```javascript
//引入 util 模块
const util = require('util');
//引入 fs 模块
const fs = require('fs');
//返回一个新的函数
let mineReadFile = util.promisify(fs.readFile);
mineReadFile('./resource/content.txt').then(value => {
  console.log(value.toString());
});
```

## 常用API
1. Promise构造函数```new Promise()```——```Promise(excutor){}```
    通过new关键字使用。
    a. Promise的参数```executor```函数:称为执行器函数——```(resolve, reject) => {}```
    b. ```resolve```函数: 内部定义成功时调用——```value => {}```
    c. ```reject```函数: 内部定义失败时调用——```reason => {}```
    
    > 说明: <span style="color:red">**executor 会在 Promise 内部立即同步调用**</span>，也就是代码```let p = new Promise((resolve,reject)=>{}```执行到new时，executor函数会立即执行。
    > 异步操作在执行器中执行，换句话说就是Promise支持同步也支持异步操作。
    
2. ```Promise.prototype.then()```方法——```(onResolved, onRejected) => {}```
    a. ```onResolved```函数: 成功的回调函数——```(value) => {}```
    b. ```onRejected```函数: 失败的回调函数——```(reason) => {}```
    > 说明: 指定用于得到成功value值的成功回调和用于得到失败reason值的失败回调，<span style="color:red">返回一个新的```promise```对象</span>。

3. ```Promise.prototype.catch()```方法——```(onRejected) => {}```
    只能指定失败的回调，不能指定成功的回调。
    a. ```onRejected```函数: 失败的回调函数——```(reason) => {}```
    > 说明: ```then()```的语法糖，相当于: ```then(undefined, onRejected)```

    b. 异常穿透使用:当运行到最后，没被处理的所有异常错误都会进入这个方法的回调函数中。

4. ```Promise.resolve()```方法——```(value) => {}```
    和```.then()```和```.catch()```不同的是，```.resolve()```方法不是属于实例对象的，而是属于```promise```这个函数对象的。它的作用就是快速得到一个promise对象，而且还能封装一个值```value```，将这个值转化为promise对象。
    a. ```value```: 成功的数据或 promise 对象，是```PromiseResult```的值。
    > 说明: 返回一个**成功/失败**的promise 对象，即```PromiseState```是成功/失败。直接改变promise状态为fulfilled或rejected。
    > 如果传入的参数为非Promise类型的对象（number、string、boolean、undefined等），则返回的结果为成功promise对象；如果参数为Promise对象，则参数的结果决定了resolve的结果。
    ```javascript
    let p = Promise.reject(new Promise((resolve, reject) => {
    resolve('OK'); }));      
    console.log(p);
```
5. ```Promise.reject()```方法: ```(reason) => {}```
    类似```Promise.resolve()```。
    a. ```reason```: 失败的原因，是```PromiseResult```的值。
    > 说明: 返回一个**失败**的 promise 对象，即```PromiseState```是失败。直接改变promise状态为rejected。代码示例同上。
    > 不管传入的是什么类型，都是失败的。

6. ```Promise.all()```方法: ```(promises) => {}```
    a. ```promises```: 包含 n 个 promise 的数组
    > 说明: 返回一个新的 promise，只有所有的 promise 都成功才成功；只要有一个失败就直接失败。
    > 返回的结果：如果最终成功，返回值就是所有的promise对象的成功结果组成的数组；最终失败，返回的就是第一个失败的那个promise对象的失败结果。
    ```javascript
let p1 = new Promise((resolve, reject) => { resolve('成功');  })
let p2 = Promise.reject('错误错误错误');
let p3 = Promise.resolve('也是成功')
const result = Promise.all([p1, p2, p3]);
console.log(result);
```

7. ```Promise.race()```方法: ```(promises) => {}```
    使用场景：ajax请求和超时回调，ajax在超时回调执行前完成，显示回调结果，否则显示请求超时。
    a. ```promises```: 包含 n 个 promise 的数组
    > 说明: 返回一个新的 promise，第一个完成的 promise 的结果状态就是最终的结果状态。
    > 如p1延时，开启了异步，内部正常是同步进行，所以p2>p3>p1，结果就是P2。
    ```javascript
    let p1 = new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve('OK');
        }, 1000);
    })
    let p2 = Promise.resolve('Success');
    let p3 = Promise.resolve('Oh Yeah');
    //调用
    const result = Promise.race([p1, p2, p3]);
    console.log(result);
```

## Promise的几个关键问题
### 一个 promise 指定多个成功/失败回调函数，都会调用吗?
当 promise 改变为对应状态时都会调用。改变状态后，多个回调函数都会调用，并不会自动停止。
```javascript
let p = new Promise((resolve, reject) => {  resolve('OK');});
//指定回调1
p.then(value => {  console.log(value); });
//指定回调2
p.then(value => { alert(value);});
```

### 改变 promise 状态（通过resolve、reject、throw）和指定回调函数（通过then、catch）的执行顺序，谁先谁后?
简单举个例子：在promise代码运行时，是```resolve()```改变状态先执行，还是```then()```指定回调先执行？
+ 都有可能, 正常情况下是先指定回调再改变状态, 但也可以先改状态再指定回调
    1. 先指定回调再改变状态(**异步**)：先指定回调--> 再改变状态 -->改变状态后才进入异步队列执行回调函数
    2. 先改状态再指定回调(**同步**)：改变状态 -->指定回调，并马上执行回调
+ 如何先改状态再**指定**回调? 
    > 注意，<span style = "color:red">**指定**并不是**执行**！</span>
    > 指定：在then中，放入成功或者失败状态，对应状态有特定的回调函数，对应关系，但是没实施

    1. 在执行器中**直接**调用 ```resolve()/reject()```，即不使用定时器等方法，执行器内直接**同步**操作
    2. 延迟调用```then()```，即在```.then()```这个方法外再包一层（如延时器）
+ 什么时候才能得到数据?
    这问题其实就等于在问回调函数什么时候执行。
    1. 如果先指定的回调，那当状态发生改变后，再去调用成功或失败的回调函数，得到数据
    2. 如果先改变的状态（立即调```resolve()```）, 那当指定回调（```then()```调用）时，就会立即调用回调函数得到数据。
    总之就是先改变状态；要是回调还没指定好，状态发生改变就去指定回调，回调要是指定好了的话就立即调用。

```javascript
let p = new Promise((resolve, reject) => {
    //1.异步写法,这样写会先指定回调,再改变状态
    setTimeout(() => {resolve('OK'); }, 1000);
    //2.这是同步写法,这样写会先改变状态,再指定回调
    resolve('OK'); 
});
p.then(value => {console.log(value);}, reason => {})
```

+ 个人理解--结合源码
    源码中，promise的状态是通过一个默认为padding的变量进行判断,所以当你resolve/reject延时(异步导致当then加载时,状态还未修改)后,这时直接进行p.then()会发现,目前状态还是进行中,所以只是这样导致只有同步操作才能成功.
    所以promise将传入的回调函数拷贝到promise对象实例上,然后在resolve/reject的执行过程中再进行调用,达到异步的目的
    具体代码实现看下方自定义promise。
    
### promise.then()返回的新promise的结果和状态由什么决定?
+ 由 ```then()```指定的回调函数执行的结果决定
+ 详细表达:
    1. 如果抛出异常， 新promise变为````rejected```, ```reason```为抛出的异常
    2. 如果返回的是非promise的任意值， 新promise变为```resolved```，```value```为返回的值
    3. 如果返回的是另一个新promise， 此promise的结果就会成为新promise的结果

```javascript
let p = new Promise((resolve, reject) => {
    resolve('ok');
});
//执行 then 方法
let result = p.then(value => {
    console.log(value);
    // 1. 抛出错误 ,变为 rejected
    throw '出了问题';
    // 2. 返回结果是非 Promise 类型的对象,新 promise 变为 resolved
    return 521;
    // 3. 返回结果是 Promise 对象,此 promise 的结果就会成为新 promise 的结果
    return new Promise((resolve, reject) => {
        // resolve('success');
        reject('error');
        });
    }, reason => {
    console.warn(reason);
});
```

### promise 如何串连多个操作任务?
1. promise 的 then()返回一个新的 promise, 可以开成 then()的链式调用
2. 通过 then 的链式调用串连多个同步/异步任务,这样就能用then()将多个同步或异步操作串联成一个同步队列

```javascript
let p = new Promise((resolve, reject) => { setTimeout(() => {resolve('OK'); }, 1000); });
p.then(value => {return new Promise((resolve, reject) => { resolve("success"); });})
.then(value => {console.log(value);})
.then(value => { console.log(value);})
```

### promise 异常传透?
+ 当使用 promise 的 then 链式调用时, 可以在最后指定失败的回调
+ 前面任何操作出了异常, 都会传到最后失败的回调中处理
```javascript
getJSON('./hong.json')
   .then(function(posts) { throw new Error('抛出异常') })
  .then(res=>console.log(res),e=>console.log('被then的错误回调捕获',e) )
   .catch(function(error) {
		 // 处理 getJSON 和 前一个回调函数运行时发生的错误
 		console.log('错误捕获: ', error);
  });
//执行结果: 被then的错误回调捕获 Error: 抛出异常

/******************** 利用异常穿透 ****************************************/
getJSON('./hong.json')
   .then(function(posts) { throw new Error('抛出异常') })
  .then(res=>console.log(res) ) //此处差异,不指定 reject 回调,利用异常穿透传到最后
   .catch(function(error) {
 		console.log('错误捕获: ', error);
  });
//执行结果:  错误捕获:  Error: 抛出异常
```
> 可以在每个then()的第二个回调函数中进行err处理,也可以利用异常穿透特性,到最后用catch去承接统一处理,两者一起用时,前者会生效(因为err已经将其处理,就不会再往下穿透)而走不到后面的catch.

### 中断 promise 链?
在关键问题"一个 promise 指定多个成功/失败回调函数, 都会调用吗?"中,可以得知,当promise状态改变时,他的链式调用都会生效,那如果我们有这个一个实际需求:我们有5个then(),但其中有条件判断,如当我符合或者不符合第三个then条件时,要直接中断链式调用,不再走下面的then,该如何?

1. 当使用 promise 的 then 链式调用时, 在中间中断, 不再调用后面的回调函数
2. 办法: 在回调函数中返回一个 pendding 状态的promise 对象

```javascript
let p = new Promise((resolve, reject) => {setTimeout(() => { resolve('OK');}, 1000);});
p.then(value => {return new Promise(() => {});})//有且只有这一个方式
.then(value => { console.log(222);})
.then(value => { console.log(333);})
.catch(reason => {console.warn(reason);});
```