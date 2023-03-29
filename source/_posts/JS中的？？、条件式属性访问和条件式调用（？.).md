---
title: JS中的表达式、条件式属性访问和条件式调用
tags:
  - JS
categories:
  - 前端
  - JS
updated: 2023-03-20 17:14:26
date: 2023-03-20 17:14:26
---

## ??

`??`在<span style='color:red'>**只有左侧的值为null或undefined的时候才使用右侧的值。**</span>
双问号后面接的就是默认值。 当然是在没有值的时候会默认 `??` 后边的值，但是有了默认值，那么 ?? 后的值也就不会在去默认了，也就是变成了赋默认值。就跟三目运算`let x = 6,y = 2;z = x>y ? x-y : x+y;`一样，不满足会执行 `:` 后边的。

> 很多情况下`??`和三目运算符用法相似，但 `??` 会取 `0` 或 `false` 为有效值, 而三元表达式不会。

示例：

```javascript
const cat = obj.detail ?. name ?? 'default name'
console.log(cat)
```

这个例子含义是查找obj中detail属性下面name属性，但是对象中没有定义所以值是undefined，这个时候`??`左侧是undefied那么就使用右侧的数据所以最终返回default name。

## 条件式属性访问

> `expression?.identifier`
> `expression?.[expression]`
> 核心：<span style='color:red'>短路。</span>
> 
> [参考链接](https://zhuanlan.zhihu.com/p/435224565)
> 
直接使用属性访问的话，如果`identifier`或`[expression]`为`null`或`undefined`，会报TypeError错误。此时使用`?.`或了`?.[]`就可以防止这种错误发生。

以`a?.b`为例。
如果a本身是null或者未定义，那么返回的结果是`undefined`而不是报错，因为a本身没有值，所以在这里短路，不会再去尝试访问b。而如果a不是`null`或`undefined`，那么返回的结果就是`a.b`的值，此时如果a没有属性b，那么依然返回undefined。

### 可选链接

更长的属性访问表达式链条更能表现`?.`的特点。

```javascript
let a = { b: null }
a.b?.c.d   //=> undefined
```

这里a和b都是有过定义的，常规的`a.b`的值会是null，但是`a.b.c`会报错。我们使用了`?.`过后，`a.b?.c`的值为undefined不会报错，`a.b?.c.d`同样是undefined不会报错，因为在c处已经短路，不会继续访问下去。但如果是`(a.b?.c).d`则会抛出TypeError。

<span style='color:red'>**如果`.?`左侧的子表达式的值为 null 或 undefined，整个表达式立即求值为undefined，不会再进一步尝试访问属性，这就是可选链接的重要特征。**</span>

### 不会发生副效应

在表达式`a?.[b][c]`中，如果a为 null 或 undefined，整个表达式立即得出undefined，其中的b和c不会被求值。也就是说如果a没有被定义，b和c的副效应（side effect）都不会发生。

```javascript
let a            //a忘记初始化
let index = 0

try{
    a[index++]  //TypeError
}catch(e){
    index       //=>1 抛出TypeError前发生了递增
}

a?.[++index]    //=> undefined
index           //=>1 因?.短路所以没有继续递增
a[index++]      //TypeError
```


## 条件式调用

可以使用`?.()`来调用函数，如果?左侧的表达式求值为 null 或 undefined，则整个表达式求值为undefined，不会报错。

若要写一个包含可选函数参数的方法，通常要先判断是否定义该参数：

```javascript
function square(x, log){   //第二个参数是一个可选的函数
    if(log){               //如果定义了可选函数就调用这个函数
        log(x)
    }
    return x * x
}
```

使用条件式调用可以简化写法：

```javascript
function square(x, log){
    log?.(x)
    return x * x
}
```

使用`?.()`来调用函数也是短路操作，如果?.左侧的值是null 或 undefined，()中的表达式不会执行。

几种写法：

```javascript
o.m()    //常规访问常规调用
o?.m()   //条件式属性访问，常规调用
o.m?.()  //常规属性访问，条件式调用
```



