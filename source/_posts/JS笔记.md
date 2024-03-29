---
title: JS笔记
updated: 2022-01-17 19:45:34
date: 2022-01-17 19:45:34
tags: [JS]
categories: [前端,JS]
---

 ***Chrome内运行***
  1. 开发者工具->Console 窗口调试 JavaScript 代码
  2. 开发者工具->Sources->Snippets选项卡-> Creat new snippet 来新建一个脚本文件  

+ JS没有任何打印或者输出的函数，只能：使用 window.alert() 弹出警告框；document.write() 方法将内容写到 HTML 文档中；innerHTML 写入到 HTML 元素； console.log() 写入到浏览器的控制台。
+ 使用 document.getElementById(id) 方法：使用 "id" 属性来标识/查找 HTML 元素，并 innerHTML 来获取或插入元素内容。使用方法如下，先
```<p id="demo">我的第一个段落</p>```指定id为“demo”，再通过```document.getElementById("demo").innerHTML = "段落已修改。";```修改指定id的内容。

下面是几个例子：
```javascript
console.time("计时器的名字");
console.timeEnd("计时器的名字"); 
console.log();

document.write();  // 实际上是通过超文本标记语言(html)输出。比如想换行，document.write(1+"\n")不行，document.write(1+"<br />")才行。
var year = prompt("请输入年份"); //prompt()：弹出一个带文本框的提示框,需要字符串做参数做为提示文字。注意返回类型为String。

Math.sqrt(i); //开方
```

## 数据类型
基本数据类型：String Number Boolean Null Undefined
引用数据类型：Object
> 主要区别：基本数据类型在栈中保存的是值，引用数据类型在栈中保存的是地址。

使用```typeof a```查看```a```的数据类型。
转义字符：```\```。```\n ```:换行  ```\t```:制表符  ```\\```:斜杠“\”。

> 在字符串中使用转义字符输出Unicode编码：```\u四位编码```，如```console.log("\u2620")```。 
> 注意这里的四位编码是16进制。
> 在网页中使用Unicode编码：```&#十进制编码```，如```<p>&#9760;</p>```。 

1. String：双引号和单引号都行，但不要混用；可以使用\作为转义字符
  + 引号需要成对出现，***不能嵌套***！
  
2. Number：包括整数和浮点数。
    + ```NaN```：特殊的数字，表示Not a Number
    + ```Number.MAX_VALUE```：最大数。比它大就是```Infinity```，都是字面量。
    + ```Number.MIN_VALUE```：最小正数。
    + 运算：整数基本保证精确。浮点元素运算可能不精确。
      
      > <span style="color:red">千万别用JS进行对精确度要求高的运算！</span>

3. Boolean

4. Null：专门表示一个为空的对象。使用```typeof```检查时返回```object```。

5. Undefined：声明了但没赋值的变量。使用```typeof```检查时返回```undefined```。

### 强制类型转换
类型转换主要指将其他数据类型转换为String Number Boolean。

1. 转换为String：
    + 调用被转换数据类型的```toString()```方法：```a = a.toString()```。
      null和undefined没有```toString()```方法。
      
    + 调用```String()```函数：```a = String(a)```。
    + **```toString()```方法和```String()```函数都不会影响原变量，返回的只是转换结果。**
    
2. 转换为Number：
    + ```Number()```函数：```a = Number(a)```。
      字符串类型有非数字的转为```NaN```，空串或者全是空格转为0。
      Undefined类型、object类型结果为```NaN```。
      Null类型结果为0。
      
        > 局限：对于```a = 123px```，想要进行```a + 10```这类情景不友好。
      
    + 一种专门解决字符串的方法
      函数```parseInt()```：将一个字符串中的 ***有效*** 整数拿出。可以用来取整。
      函数```parseFloat()```：将一个字符串中的 ***有效*** 小数拿出。
      如果是非String，会转换成string再处理。
      
        > 可以在parseInt()中传递第二个参数指定进制：```parseInt(a,10)```
  
3. 转换为Boolean：```Boolean()```函数
    + 数字：除了0和NaN都是```true```；字符串：除了空串都是```true```；null和undefined都是```false```
    + 总之，除了0、-0、NaN、‘’、null和Undefined都为true。
    + ***可以对任意数据类型取反两次转换为Boolean:```a = !!a```。***```!!```是一种强制类型转换，作用为把变量转换成“等价”的布尔值。

### 引用数据类型
+ 栈内存和堆内存
    + 栈内存：JS中的<span style="color:red">变量都保存到栈内存</span>，基本数据类型的<span style="color:red">值</span>直接在栈内存中存储。值与值之间独立存在，修改一个变量不会影响其他变量。
    + 堆内存：JS中的<span style="color:red">对象都保存到堆内存</span>，每创建一个新的对象就会在堆内存中创建一个新的空间。这种情况下<span style="color:red">变量保存的是对象的内存地址（对象的引用）。</span>
      如果两个变量保存的是同一个对象引用，则当一个通过一个变量修改<span style="color:red">属性</span>时，另一个也会受到影响。如果直接修改变量的<span style="color:red">值</span>，比如```obj2 = null```，那么obj不受影响。
    + 当比较两个基本数据类型的值时，就是比较值；但两个引用数据类型比较的是对象的内存地址。

## 作用域
### 全局作用域
全局作用域在页面打开时创建，在页面关闭时销毁。直接编写在script标签中的JS代码都在全局作用域。
全局作用域中有一个全局对象```window```，它由浏览器创建，代表浏览器窗口，我们可以直接使用。全局作用域中创建的变量都会作为```window```对象的属性保存，创建的函数都会作为```window```对象的方法保存。
全局作用域中的变量都是全局变量，在页面的任意部分都可以访问到。

### 函数作用域

调用函数时创建函数作用域，函数执行完毕以后函数作用域销毁。
每调用一次函数就会创建一个新的函数作用域，他们之间互相独立。
当在函数作用域操作变量时，会优先在自身作用域中寻找，如果有就直接使用。如果自身作用域没有，按照就近原则使用上级作用域中的同名变量。如果全局作用域中依然没有就会报错。要在函数中精确访问全局变量可以使用```window```对象。

<span style="color:red">定义形参就相当于在函数作用域中声明了变量。</span>函数中定义形参a就相当于var一个a。

<span style="color:red">在函数中，不使用var声明的变量都会成为全局变量！前提是这个函数先被调用了。</span>

### 变量和函数的声明提前（提升）

变量的声明提前：使用```var```关键字声明的变量，会在所有代码执行之前被声明（但不会赋值，使用的时候再复制），也就是预编译先找到```var a```，让变量```a=undefined```。<span style="color:red">但如果声明变量不是用的```var```关键字，则变量就不会被声明提前。</span>

函数的声明提前：使用函数声明形式创建的函数```function 函数(){}```会在所有的代码执行之前就被创建，所以我们可以在函数声明前就调用函数。这样的函数声明写在哪里都一样。
但使用函数表达式创建的函数```var 函数名 = function(){}```不会被声明提前，所以不能在声明前调用。因为根据变量的声明提前原理，由于使用了```var```关键字，所以函数名被赋值为undefined，只有在使用时才会被赋予具体值。

在函数作用域中也有声明提前的特性，使用```var```关键字声明的变量会在函数中所有代码执行之前被声明。使用函数声明形式创建的函数也会在函数中所有的代码执行之前执行。

### this
解释器在调用函数每次都会向函数内部传递一个隐含参数，这个隐含的参数就是```this```。```this```指向的是一个对象，该对象称为函数执行的上下文对象。根据函数 **调用方式** 的不同，```this```会指向不同的对象：
1. 以函数形式调用时，```this```永远都是```window```
2. 以方法形式调用时，```this```就是调用方法的对象
3. 以构造函数形式调用时，```this```就是新创建的对象
3. 在事件的响应函数中，响应函数是给谁绑定的```this```就是谁

总之就是```this```指向当前调用函数的对象。

## 运算

任何数和NaN运算的结果都是```NaN```。
除拼串，对任何非Number的值会转换成Number。

### 算术运算
+ +：对几个字符串使用是拼串操作。```var a = "123"+"456"```，结果为```123456```。事实上，***任何值和字符串相加都会转换成字符串并进行拼串操作***。

    > 隐式类型转换：可以利用这一特点将一个任意数据类型转换为```String```，只需要任意的数据类型 + 一个""即可：```a = a + ''```。

+ \-  \*  /：除了“ + ”的其他运算符号（-  *  /），在算式中出现的变量都会转化成数字再计算。
  
  > 隐式类型转换：可以利用这一特点将一个任意数据类型转换为```Number```，只需要任意的数据类型 ```-0```;```*1```;```/1```即可。原理和```Number()```一样，使用更简单。

+ 一元运算符：全换为Number类型再运算。```+ -```, 正号" + "不会对数字产生任何影响, 负号“-”可以对数字进行符号取反。 
  
  > 隐式类型转换：可以利用这一特点将一个任意数据类型转换为```Number```，只需要任意的数据类型 ```a = +a```即可。如```a = 1 + +a + 2```。

### 逻辑运算
JS中的&& 和 || 是短路与/或而不是逻辑与/或。
对于非布尔值：会先将其转换为布尔值再运算，并且 ***返回原值*** 。
+ ```&&```：两值运算，若第一个为true，返回第二个值；第一个为false，直接返回第一个值。
+ ```||```：两值运算，若第一个为true，直接返回第一个值；第一个为false，返回第二个值。

+ ```==``` 和 ```!=```：（不）相等运算。若两个值类型不同，会转化成相同类型再比较，大部分情况是转化为数字。
    + Undefined衍生自null，所以这两个值作相等判断时会返回true
    + NaN不和包括它本身任何值相等。可以通过```isNaN()```函数判断一个值是否为NaN。
+ ```===``` 和 ```!==```：（不）全等运算。和相等的区别在于它不会做类型转换。若类型不同直接返回。


### 关系运算
指```> >= < <= =```。会转化成数字再比较。
任何值和NaN比较都是false。
> 若比较符号的两侧都是字符串，不会转换为数字，而会分别比较字符串中的 ***Unicode编码***，一位一位比较，两位一样就比下一位。
> 如果比较两个字符类型的数字时，可能得到不可预期的结果。所以比较两个字符串类型的数字时，***一定要转型***。
> 可以利用该特性对英文排序。

### 条件运算
语法：```条件表达式？语句1:语句2```。先执行条件运算符，判断执行语句1还是2，并返回执行结果。
若条件表达式的结果是非布尔值，会将其转化为布尔值再运算。

## 函数和代码块
调用函数时 **解释器不会检查实参的类型** ，实参可以是任意数据类型。所以要注意如果有可能接收到非法的参数就需要对参数进行类型检查。同样， **解释器不会检查实参的数量** ，多余实参不会被赋值，如果实参数量少，则没有对应实参的形参将是undefined。

### 普通函数和箭头函数
两种写法分别为```data:function(){}```和  ```data:()=>{}```。
一般在对象里写方式会删掉function，删掉冒号：```data(){}```
区别：箭头函数没有自己的this，只会向外找，找到的是window。

### 函数中的**return**
在函数中，```return```后可以跟任意类型的值。```return```后的语句都不会执行；如果```return```语句后不跟任何值或者函数中不写```return```就相当于返回一个undefined。

> 注意```my fun()```和```fun```的区别：
> ```my fun()```：是调用函数，相当于使用了函数 **返回值** 
>```myfun```：是函数对象，相当于直接使用了函数 **对象** 

### 函数声明、立即执行函数和代码块
使用**函数声明**或**函数表达式**来创建函数：
```javascript
// 函数声明
function 函数名(形参1,形参2,...,形参n){
  语句...
}

// 函数表达式：创建一个匿名函数,再将这个匿名函数对象赋值给一个变量
// 此时，语句本质上是一个赋值语句，因此最好在末尾加分号。
var 函数名 = function(形参1,形参2,...,形参n){
  语句...
};
```

使用 **立即执行函数** 使函数定义完立即被调用。立即执行函数往往只会执行一次。

```javascript
(function(a,b){
  // 如果外围不加括号，解释器会将大括号中的内容识别为一个代码块而无视前面的声明
  console.log("a=" + a);
  console.log("b=" + b);
})(123,456)
```

一个```{}```中的内容称为一个代码块，一个代码块中的内容要么都执行，要么都不执行。
> JS中的代码块 ***只有分组作用***！块中的内容块外也完全可见，并不隔离。


### 条件判断/条件分支/switch/for语句

```javascript
// if语句
if(条件表达式){ 语句... }
else if{ 语句... }
else{ 语句... }

// switch语句。依次将case后的表达式和switch后的条件表达式进行全等比较。
switch(条件表达式){
  case 表达式；
    语句...
    break; 
  default:
    语句...
    break;
}

// while。判断一次执行一次。可以使用break终止循环。
while(条件表达式){ 语句... }
// do...while。执行一次判断一次。也就是while先判断再执行，do...while先执行再判断。do...while可以保证循环体至少执行一次。
do{ 语句... }while(条件表达式)

// for语句。
for(初始化表达式；条件表达式；更新表达式){ }
```

### ***break***和***continue***（和***return***）

可以为循环语句加上一个label标识当前循环。比如创建一个label为```outer```的循环：```outer:循环语句```

+ ```break outer```：结束循环。立即终止label=```outer```的循环。默认终止本 ***层*** 循环。
  
  > 只能对循环和switch语句使用!
  
+ ```continue outer```：跳过当 ***次*** 循环，继续下一次循环。其它和```break```一样。
+ ```return```：结束整个函数。

### 函数的方法：call()和apply()——修改函数对象的this
这两个方法都是函数对象的方法，需要通过函数对象（不加()）来调用。当对函数调用call()和apply()时都会使函数执行。
在调用call()和apply()时，可以将一个对象指定为第一个参数，**此时这个对象将会成为函数执行时的this。**

call()方法可以将实参在对象后依次传递：```fun.call(obj,3,4) // a=3，b=4``` 
apply()方法需要将实参封装到一个数组中统一传递：```fun.apply(obj,[3,4]) // a=3，b=4``` 

### arguments
在调用函数时，浏览器每次都会传递进两个隐含的参数：函数的上下文对象this和封装 **实参** 的对象```arguments```。```arguments```是一个类数组对象，它也可以通过索引来操作数据，也可以获取长度。在调用函数时我们传递的实参都会在```arguments```中保存，```arguments.length```可以用来获取实参的长度。即使不定义形参也可以通过```arguments```来使用实参，只不过比较麻烦；```arguments[0]```表示第一个实参，以此类推。
> 不管定不定义形参，实参都会在```arguments```中保存！

```javascript
function fun(){
  console.log(arguments);  // [object arguments]
  // 是类数组对象，不是数组对象
  console.log(arguments instanceof Array);  // false
  console.log(Array.isArray(arguments));  // false
}
```

另外，```arguments```里面还有一个属性叫```callee```，这个属性对应一个函数对象，就是当前正在指向的函数对象。


## 数组

也是对象，不同的是普通对象使用字符串作为属性名，数组使用数字作为索引操作元素。
数组的存储性能比普通对象好，在开发中经常使用数组来存储一些数据。

```javascript
// 可以在创建时就指定数组中的元素。使用```typeof```检查数组时返回object。
// 创建数组对象————使用构造函数和使用字面量创建数组：
var arr = new Array(1,20,30); // 只传一个正整数时创建的是长度为指定值的空数组。
var arr1 = [1，2，3，10]; // 常用

// 向数组中添加元素。语法：数组[索引] = 值
// 读取数组中的元素：数组[索引]。如果读取不存在的索引返回undefined。
arr[0]=10;

// 获取数组长度。对于连续数组返回元素个数，非连续数组返回最大索引 + 1。
console.log(arr.length);
// 修改length。修改的length大于原长，多出的部分会空出来；小于时多出的元素会被删除。
arr.length = 5;
// 技巧：向数组的最后添加元素
arr[arr.length] = 66;
```

### 数组的遍历
通常都是用for循环。

JS还提供了一个方法：```forEach()```。需要注意该方法只支持IE8以上的浏览器。```forEach()```方法需要一个函数作为参数，通常不会为了它去专门创建一个全局变量，而是使用匿名函数（像这种函数，由我们创建但不是由我们调用的，称为回调函数。）。

数组中有几个元素就会执行几次，每次执行时浏览器会将遍历到的元素以实参的形式传递进来，我们可以定义形参来读取这些内容。
浏览器会在回调函数中传递三个参数：第一个是当前正在遍历的元素，第二个是当前正在遍历的元素的索引，第三个是当前正在遍历的数组。
```javascript
arr.forEach(function(value,index,obj){ })
```

### 增减开头/末尾元素：push()和pop()  unshift()和shift()
push()方法向数组的末尾添加一个或多个元素，可以将要添加的元素作为方法的参数传递，这样这些元素将会自动添加到数组的末尾。同时将新数组的**长度**作为返回值返回。
pop()方法删除数组的最后一个元素，同时将被删除的**元素**作为返回值返回。

unshift()方法向数组开头添加一个或多个元素，同时将新数组的**长度**作为返回值返回。向前面插入元素后，其他元素的索引会依次调整。
shift()方法删除数组的第一个元素，同时将被删除的**元素**作为返回值返回。

### 提取元素：slice()  splice()
slice()方法用来从数组中提取指定元素。该方法不会改变原数组，而是将截取到的元素封装到一个新数组中返回。
参数：1.截取开始的位置的索引；2.截取结束的位置的索引（左闭右开）。索引可以传递一个负值，表示从后往前计算（-1是倒数第一个，-2倒数第二个）。第二个参数可以省略不写，此时会截取从开始索引往后的所有元素。

splice()用来从数组中删除（并替换/插入）指定元素。该方法会改变原数组：将指定元素从原数组中删除，并将被删除的元素作为返回值返回。
参数：1.截取开始的位置的索引（包含）；2.删除的数量；3.第三个及以后的参数可以传递新元素，将它们按顺序插入到开始位置索引的前面。

### 连接数组：concat()
concat()方法用来连接两个或多个数组，该方法不会影响原数组，并将新数组返回。

### 转换为字符串：join()
join()方法用来将数组转换为一个字符串，该方法不会影响原数组，并将转换后的字符串作为结果返回。
可以指定一个**字符串**作为参数，这个字符串将会成为数组中元素的连接符。如果不指定则默认使用逗号作为连接符。

### 反转数组：reverse()
reverse()方法用来反转数组，该方法会直接修改原数组。

### 排序：sort()
sort()方法用来对数组中的元素进行排序，该方法会直接修改原数组。
默认按照Unicode编码进行排序。即使对纯数字数组，sort()方法也会按照Unicode编码进行排序。所以对数字排序时可能会得到错误结果。

> 浏览器不同可能导致结果相反

可以在sort()中添加一个回调函数指定排序规则。回调函数需要定义两个形参，浏览器将会分别使用数组中的元素作为实参去调用回调函数，使用哪个元素调用不确定，但肯定的是在数组中a一定在b前面，浏览器会根据回调函数的返回值决定元素的顺序，如果返回一个大于0的值，则元素会交换位置；返回一个小于或等于零的值，元素位置不变。
如果需要升序排列，返回a-b；如果需要降序排列，返回b-a。

```javascript
arr.sort(function(a,b){ return a-b;})  // 升序排列
```


## 对象

1. 内建对象：由ES标准中定义的对象。在任何ES的实现中均可使用。如Math String Number Boolean Function Object
2. 宿主对象：由JS的运行环境提供的对象。主要指由浏览器创造的对象。如BOM（浏览器对象模型） DOM（文档对象模型）。```console```、```document```也属于这一类。
3. 自建对象：由开发人员自己创建。


属性名和属性值是名值对。名与值之间使用 ```:``` 连接，多个名值对之间使用 ```,``` 隔开。***对象中的最后一个属性之后不要写 ‘```,``` ’。***读取对象中的属性值可以使用```对象.属性名```的方式。

读取对象中没有的属性，不会报错而是会返回```undefined```。
如果使用了特殊的属性名，需要用```对象["属性名"] = 属性值```的方式操作。<span style="color:red">中括号[ ]的特点就是可以传递一个变量。</span>

> <span style="color:red">使用[ ]操作对象更加灵活。在[ ]中可以直接传递一个变量，这样变量值是多少，就会读取变量值那个属性：</span>
>
> 比如```var n = "hi"; obj[n]="你好"; console.log(obj[n]);```就会输出“你好”。


+ 删除属性：```delete 对象.属性名```。 
+ 使用```in```检查对象中是否含有某个属性时，如果对象中没有但原型中有，也会返回true。
+ 可以使用对象的```hasOwnProperty()```方法检查对象自身中是否含有该属性，只有当对象自身含有属性时才会返回true。

### 方法
如果一个函数作为对象的属性保存，我们称这个函数为对象的**方法**。

使用```for ... in```语句枚举对象中的属性。对象中有几个属性循环体就执行几次。每次执行时，会将对象中的一个属性的 **名字** 赋值给变量。语法：```for(变量 in 对象){}```。
```javascript
for(var n in obj){
  console.log(“属性名” + n); // 属性名：name 属性名：age
  console.log(“属性值” + obj[n]); // 属性值：Bob 属性值：18
}
```

+ 使用工厂方法创建对象：可以大批量地创建对象，尤其是具有相同属性名、不同属性值的。
```javascript
function createPerson(name,age,gender){
  // 创建一个新的对象
  var obj = new Object();
  // 向对象中添加属性
  obj.name = name;
  obj.age = age;
  obj.gender = gender;
  obj.sayName = function(){
    alert(this.name);
  };
  // 将新的对象返回
  return obj;
}
var obj2 = createPerson("张三"，18，“男”)；
```

### 构造函数
使用工厂方法创造的对象使用的构造函数都是Object，所以创建的对象都是Object这个类型，导致无法区分多种不同类型的对象。

构造函数就是一个普通函数，创建方式和普通函数一样，不同的是构造函数习惯首字母大写。
构造函数和普通函数的区别：调用方式不同。普通函数是直接调用，而<span style="color:red">构造函数需要使用```new```关键字调用。</span>

使用同一个构造函数创建对象称为一类对象，也将一个<span style="color:red">构造函数</span>称为一个<span style="color:red">类</span>。将通过同一个构造函数创建的<span style="color:red">对象</span>称为该类的<span style="color:red">实例</span>。使用```instanceof```可以检查一个对象是否是一个类的实例，语法：```对象 instanceof 构造函数```。如果是，返回```true```，否则返回```false```。

> 所有对象都是Object的后代，因此任何对象和Object做instanceof检查时都会返回true。

```javascript
function Person(name,age){
  this.name = name;
  this.age = age;
  this.sayName = function(){
    alert(this.name);
  }
}
var per = new Person("Bob",18);
per.sayName();
console.log(per.sayName);
```

+ 构造函数的执行流程：
  1. **立刻** 创建一个新的函数对象。一出现```new```，就会在堆内存开辟出一个新的内存空间。这片空间保存Person()函数中的内容。
  2. 将新建的对象设置为构造函数中的```this```。在构造函数中可以使用```this```来引用新建的对象。也就是新开辟出的堆内存空间的地址赋值给变量per，this保存着per的地址，指向per这块对象内存。(this 是保存在栈中的, 执行构造函数的时候, this也保存了那片区域的地址)
  3. 逐行执行函数中的代码。（只有这一步是我们写的，其他都是浏览器干的）
  4. 将新建的对象作为返回值返回。

### 垃圾回收（Garbage Collection，GC）
当一个对象没有任何的变量或属性对它进行引用，将永远无法操作该对象，这种对象就是垃圾，存在过多会占用大量的内存空间，必须进行清理。

JS中有自动的垃圾回收机制，会自动将这些垃圾对象从内存中销毁，我们不需要也不能进行垃圾回收的操作。
我们需要做的只是要将不再使用的对象设置为```null```。

## 原型对象

在上面的Person()构造函数中为每一个对象都添加了一个sayName方法，而且这个方法是在构造函数内部创建的，也就是构造函数每执行一次就会创建一个新的sayName方法，（说到底每次执行构造函数在堆空间中开辟的内存都不同），也就是所有实例的sayName都是唯一的。如果像这样将sayName方法在全局作用域中定义（这里叫它fun）：```function fun(){alert(this.name);}```，则会污染全局作用域的命名空间，而且定义在全局作用域中也很不安全。

### 显式原型```prototype```和隐式原型```__proto__```
显式原型（函数）：我们所创建的每一个函数，解析器都会向函数中添加一个属性```prototype```（例外：通过Function.prototype.bind方法构造出来的函数没有prototype属性）。这个属性指向函数的原型对象。其主要作用是实现基于原型的继承与属性的共享。
隐式原型（实例）：可以通过```__proto__```（两边都是两个下划线）访问对象的内置属性```prototype```。其主要作用是构成原型链，同样用于实现基于原型的继承。比如访问obj对象中的x属性时，如果在obj中找不到就会沿着```__proto__```依次查找。```__proto__```指向创建这个对象的函数的显式原型。

显式原型```prototype```和隐式原型```__proto__```的区别在于，```prototype```是构造函数的属性，而```__proto__```是对象的属性。
无论是函数身上的显式原型属性，还是实例身上的隐式原型属性，它们都指向了同一个对象：**原型对象**。通过显式原型链给原型(或者原型的原型)添加属性，通过隐式原型链获取原型的属性，从自身沿着原型链一直找直到window的原型为空。

<span style='color:red'>实例的隐式原型属性，永远指向自己缔造者的原型对象！</span>

![原型](yuanxing.png)

如果函数作为普通函数调用，```prototype```没有任何作用；当函数以构造函数的形式调用时，它所创建的对象中都有一个隐含的属性指向<span style="color:red">该构造函数的原型对象。</span>也就是```构造函数.prototype == 所有该构造函数的实例._proto_```。

原型对象就相当于一个公共的区域，所有一个类的实例都可以访问到这个原型对象。我们可以将对象中共有的内容统一设置到原型对象中。当我们访问对象的一个属性或方法时，它会先在对象自身中寻找，如果没有则会去原型对象中寻找，找到了就直接使用。

以后我们创建构造函数时可以将这些对象共有的属性和方法统一添加到构造函数的原型对象中，这样不用为每一个对象添加，也不会影响到全局作用域，就可以使每个对象都具有这些属性和方法了。

```javascript
// 向Person的原型中添加属性a
Person.prototype.a = 123;
// 向Person的原型中添加一个方法
Person.prototype.sayName = function(){
  alert(this.name);
}

var per = new Person("Bob",18)
per.sayName();
```

### 原型对象的原型
原型对象也是对象，所以它也有原型。
当我们使用一个对象的属性或方法时，会先在自身中寻找，自身中如果有，则直接使用；如果没有则去原型对象中寻找，如果原型对象中有，则使用；如果没有则去原型的原型中寻找。直到找到Object对象的原型————虽然Object对象的原型没有原型，但它也有一个__proto__属性，这个属性对应的值就是null。如果在Object中依然没有找到，则该对象.属性返回undefined。（null是没找到原型，undefined是没找到原型的属性）

在看原型链时，只要看到```toString()```方法，就证明这个已经到了Object的原型对象。

```javascript
// 原型。实例._proto_=构造函数.prototype，即Person的prototype
console.log(per.__proto__);
// 原型的原型。Person.prototype的_proto_，是Object的prototype
console.log(per.__proto__.__proto__); //[Object Object]

console.log(per.__proto__.__proto__.hasOwnProperty("hasOwnProperty")); //true

// 原型的原型的原型。Object的原型没有原型。
console.log(per.__proto__.__proto__.__proto__); // null
```

### toString()
当我们直接在页面中打印一个对象时（console.log)，实际上是输出的对象的```toString()```方法的返回值。如果我们希望在输出对象时不输出[Object Object]，可以为对象（或原型）添加一个```toString()```方法。
> 把prototype写在构造方法前面才能覆盖```toString()```方法！

```Javascript
// 它们的输出都是[Object Object]（除了Chrome）
console.log(per);
console.log(per.toString());

// 修改per对象原型的toString
Person.prototype.toString = function(){
  return "Person[name=" + this.name + ",age=" + this.gender + "]";
}
console.log(per);  // 现在的输出是"Person[name=Bob,age=18]"
```

## Date对象
使用```var d = new Date()```创建Date对象。
如果直接使用构造函数创建一个Date对象，则会封装为当前代码执行的时间
使用```var d2 = new Date(月/日/年 时：分：秒)```创建指定时间的Date对象。需要在构造函数中传递一个表示时间的字符串作为参数。

### getdate()，getday()，getmonth()，getFullyear()
使用```getdate()```获取当前日期对象的日是多少，getFullyear()同理。getday()获取当前日期对象是周几。getmonth()获取当前日期对象的月份，返回一个0-11的值，0表示一月。

### 时间戳：getTime()和Date.now()
使用```getTime()```获取当前日期对象的时间戳，即从格林威治标准时间的1970年1月1日0时0分0秒到当前日期所花费的毫秒数。
> 时区不同，计算结果也不同。在CN，1970年1月1日0时0分0秒的时间戳并不为0。
计算机底层在保存时间时使用的都是时间戳。

使用```Date.now()```获取当前时间的时间戳。

## Math对象
**Math和其他的对象不同，它不是一个构造函数！**
它属于工具类，不用创建对象，它里面封装了数学运算相关的属性和方法。

```javascript
Math.ceil()    // 向上取整
Math.floor()   // 向下取整
Math.round()   // 四舍五入取整

Math.random()  // 生成[0，1)的随机数，可以乘x生成[0，x)的随机数
Math.round(Math.random()*(y-x)+x)  // 生成[x，y )的随机数

Math.pow(x,y)  // 返回x的y次幂
Math.sqrt()  // 返回平方根
```

## 包装类
JS中提供了三个包装类，可以将基本数据类型的数据转换为对象。

+ ```String()```：可以将基本数据类型的字符串转换为String对象
+ ```Number()```：可以将基本数据类型的字符串转换为Number对象
+ ```Boolean()```：可以将基本数据类型的字符串转换为Boolean对象

但是注意，实际应用中不会使用基本数据类型的对象（如```var num = new Number(3)```），因为使用它们可能在比较时带来不可预期的结果。

方法和属性只能添加给对象，不能添加给基本数据类型。但```var s = 10;s = s.toString();s.hello = "11"```不会报错，原因在于当我们对一些基本数据类型的值去调用属性和方法时，浏览器会临时使用包装类将其转换为对象，再调用对象的属性和方法。调用完后还是基本数据类型。

## String对象的相关方法
字符串在底层是以字符数组的形式保存的。比如它也有length属性，可以用来获取字符串的长度。

除非特殊说明，字符串的大部分方法都不会改变原字符串。

+ ```charAt()```：根据索引返回字符串中指定位置的字符。作用同中括号[ ]。
+ ```charCodeAt()```：获取指定位置字符的Unicode编码。
+ ```formCharCodeAt()```：根据字符编码去获取字符。需要用构造函数去调用：```String.formCharCodeAt()```。
+ ```concat()```：连接两个或多个字符串，作用同加号。
+ ```indexOf()```：检索一个字符串中是否含有指定内容。如果含有该内容，会将其**第一次**出现的索引作为返回值返回。如果没找到则返回-1。可以指定第二个参数指定**开始查找**的位置。
+ ```lastIndexOf()```：用法同上，不同的是上一个从前往后，这个从后往前找。返回最后出现的下标。也可以指定开始查找的位置。
+ ```slice()```：从字符串截取指定内容并将截取到的内容返回。第一个参数是开始位置的索引，第二个是结束位置的索引，左闭右开。省略第二个参数就会截取后面所有的。传递负数将会从后边计算，-1是倒数第一个，以此类推。
+ ```subString()```：和```slice()```完全一样，不同的是这个方法不能接受负值作为参数，如果传了负值自动按0处理，而且他还自动调整参数的位置，如果第二个参数小于第一个就自动交换。
+ ```split()```：将字符串拆分为数组。需要一个字符串作为参数，将会根据该字符串去拆分数组。如果传递空串作为参数，则会将每个字符都拆分为数组中的一个元素。

## 正则表达式
> 一些处理常见任务的正则表达式可以直接去网上搜索。

正则表达式（reg，Regular Expression，又称规则表达式）用于定义一些字符串的规则，计算机可以根据正则表达式检查一个字符串是否符合规则，将字符串中符合规则的内容提取出来。
通过```var 变量 = new RegExp("正则表达式"，"匹配模式");```创建正则表达式的对象。还可以使用字面量创建正则表达式```var 变量 = /正则表达式/匹配模式```。使用字面量方式创建更加简单，使用构造函数创建更加灵活。使用typeof检查正则对象返回object。
使用```test()```方法检查一个字符串是否符合正则表达式的规则，如果符合返回true。

使用竖线```|```表示或者的意思，中括号```[]```里的内容也是或的意思，[ab]==a|b。```[a-z]```表示任意小写字母，大写字母同理。```[A-z]```表示任意字母（严格来说ASCII标里A-z中间有六个其他字符）。可以使用```/a[bde]c/```检查一个字符串中是否含有abc或adc或aec。```[^ ]```表示除了，只要有除了中括号内符号的其他符号就是true。

在正则表达式中使用```\```作为转义字符，用```\.```表示```.```，用```\\```表示```\```。正则表达式中的```.```，```\```等特殊字符需要在前面加上```\```进行转义。

> 注意使用构造函数时，由于它的参数是一个字符串，而```\```是字符串中的转义字符，如果要使用```\```也需要使用```\\```代替。

还有一些带转义字符的**元字符**。
1.```\w```：任意字母数字下划线，相当于[A-z0-9_]。```\W```：和```\w```相反，表示[^A-z0-9_]。（word）
2.```\d```：任意数字。```\D```：除了数字。（digital）
3.```\s```：空格。```\S```：除了空格。（space）
4.```\b```：单词边界。B同理。（bound）比如检查一个字符串中是否含单词child可以使用```reg = /\bchild\b/```。

> 其实```\b```的作用不只是检查空格，实际上是检查以“半角英数字+下划线”构成的词汇前后存在“非词汇构成元素”（即\W）的情况，比如“hello%child#”，“hello【child】”这些都能排查出来。

在构造函数中可以传递一个匹配模式作为第二个参数。```i```：忽略大小写；```g```：全局匹配模式。可以为一个正则表达式设置多个匹配模式，顺序无所谓。

```javascript
var reg = new RegExp("a"，"i");  // 检查一个字符串中是否含a/A
var reg = /a/i; // 和上面一行效果相同
var str = "a";
console.log(reg.test("a"));
```

### 正则相关的字符串方法
使用```split()```：将字符串拆分为数组。需要一个字符串作为参数，将会根据该字符串去拆分数组。如果传递空串作为参数，则会将每个字符都拆分为数组中的一个元素。可以传递一个正则表达式作为参数，这样方法将会根据正则表达式去拆分字符串。**该方法即使不指定全局匹配也会全部拆分。**

使用```search()```：搜索字符串中是否含有指定内容。如果搜索到指定内容，返回第一次出现的索引；如果没有搜索到就返回-1。可以接受一个正则表达式为参数，根据正则表达式去检索字符串。```str = [hello abc afc]; result = str.search(/a[bef]c/)```。**该方法即使指定全局匹配也只查找第一个。**

使用```match()```：根据正则表达式从一个字符串中将符合条件的内容提取出来。默认情况下match只会找到第一个符合要求的内容，找到以后就停止检索。可以设置正则表达式为全局匹配模式，这样就会匹配到所有的内容。match会将匹配到的内容封装到一个数组中返回，即使只查询到一个结果。

使用```replace()```：将字符串中的指定内容替换为新的内容。第一个参数：被替换的内容，可以接受一个正则表达式作为参数；第二个参数：新的内容。默认只会替换第一个，指定匹配模式含g可以实现全部替换。**第二个参数指定为空串则会删除指定内容。**
> 利用```replace()```方法去除字符串```        he  llo     ```的空格
> 去掉开头的空格：```str = str.replace(/^\s*/,"");```
> 去掉结尾的空格：```str = str.replace(/\s*$/,"");```
> 去掉开头和结尾的空格：```str = str.replace(/^\s*｜\s*$/g,"");```。其中的两个星号换成加号也可。

### 量词
**使用量词设置一个内容出现的次数。**需要注意的是量词只对它前边的一个内容起作用，通过括号指定需要重复的组合。
{n}，如```{(ab)n}```，ab正好出现n次。
{m,n}，如```{ab{m,n}c}```，b出现m到n次。
{m,}，出现m次以上。
+，至少一个。相当于{1,}。
*，0个或多个，相当于{0,}。
？，0个或一个，相当于{0,1}。
^，表示开头。如```{^a}```，以a开头。
$，表示结尾。如```{a$}```，以a结尾。
**如果在正则表达式中同时使用^和$则要求字符串必须完全满足正则表达式。**

比如创建一个用来检查字符串是否为合法手机号的正则表达式。规则：以1开头+第二位为3-9的任意数字+含第三位的九个任意数字。```^1[3-9][0-9]{9}$```

### 邮件的正则
例如hello.nihao@abc.com.cn
> 注意量词的使用！

规则：任意字母数字下划线(3位以上)  .任意字母下划线(可选)  @  任意字母数字  .任意字母（2-5位）  .任意字母（2-5位）。
分别对应：```/\w{3,}/```   ```/(\.\w+)*/```  ```/@/```  ```/[A-z0-9]/+```  ```/\.[A-z]{2,5}/```  ```/\.[A-z]{2,5}/```
组合起来：```/\w{3,}(\.\w+)*@[A-z0-9]+(\.[A-z]{2,5}){1,2}/```。但这样写存在问题，还需要加开始和结束符号做严格的限制：```/^\w{3,}(\.\w+)*@[A-z0-9]+(\.[A-z]{2,5}){1,2}$/```。

## DOM
Document Object Model，文档对象模型。用处就是可以让人用JS对HTML文档进行操作。
文档：整个HTML网页文档
对象：网页中的每一个部分都转换成了一个对象。（转换为对象后就可以使用纯面向对象的方式操作网页）
模型：使用模型表示对象间的关系，方便获取对象。

节点Node，是构成网页的最基本的组成部分，网页中的每个部分都可以称为是节点，如html标签、属性、文本、注释、整个文档等。虽然都是节点但具体类型不同。节点类型不同属性和方法也不尽相同。常用节点分为四类：

+ “文档节点”：整个HTML文档
+ “元素节点”：HTML文档中的HTML标签（元素就是标签）
+ “属性节点”：元素的属性
+ “文本节点”：HTML标签中的文本内容
![常用节点](jiedian.png)
可以使用```innerHTML```获取元素内部的HTML内容；```innerText```获取元素内部的文本内容，它和innerHTML类似，不同的是它会自动将HTML标签去除。

```nodeName```，```nodeType```，```nodeValue```是所有节点都有的属性。具体如下图。可以通过```nodeType```判断节点类型。
![节点属性](shuxing.png)
浏览器已经为我们提供了文档节点对象，文档节点代表的是整个网页。这个对象是window属性，可以在页面中直接使用。

```html
<button id="btn" onclick="alert('按钮已点击')">我是一个按钮</button>
<script>
  // 获取到button对象
  var btn = document.getElementById("btn");
  console.log(btn);  // [object HTMLButtonElement]
  // 修改按钮的文字
  btn.innerHTML = "新的文字内容"
</script>
```

### 文档的加载
浏览器加载页面时按照自上而下的顺序，读取到一行运行一行。如果将script标签写到页面的上边，那么在代码执行时页面还没有加载，页面没加载那么DOM对象也没加载，会导致无法获取到DOM对象。将js代码编写到页面下部就是为了可以在页面加载完毕后再执行js代码。

onload事件对应的响应函数会在整个页面加载完成后才会触发。可以为window绑定一个onload事件```window.onload = function9){}```以确保代码执行时所有的DOM对象已经加载完毕了，不会出现获取不到的情况。如果把js代码写在html文档的head中可以把它们都放在onload事件内。


### 事件
文档或浏览器窗口中发生的一些特定交互瞬间。可以在事件对应的属性中编写一些js代码。
类似上文中```<button id="btn" onclick="alert('已点击')">我是按钮</button>```的写法称为结构和行为耦合，不方便维护，不推荐使用。
可以为按钮的对应事件绑定处理函数的形式来响应事件。当事件被触发时，其对应的函数将会被调用。像onclick这种为单机事件绑定的函数称为单机响应函数。
```html
<button id="btn">我是一个按钮</button>
<script>
// idStr：要绑定单机响应函数的对象的id属性值；fun：事件的回调函数
function.myClick(idStr,fun){
  // 获取到button对象
  var btn = document.getElementById(idStr);
  btn.onclick = fun;
};
myClick(mybtn,function(){
  // 获取id为element1的元素
  var ele1 = document.getElementById("element1");
  // 返回#element1的前一个兄弟节点（如果HTML文档内有换行，也可能获取到空白文本）
  var ps = ele1.previousSibling;
  alert(ps.innerHTML);
  // 使用previousElementSibling获取前一个兄弟元素，IE8以下不支持
  var pe = ele1.previousElementSibling;
  
});
</script>
```

常用的函数还有```onscroll```，该函数会在元素的滚动条滚动时触发；```onmousemove```，该事件会在鼠标在元素移动时触发；

### 事件对象
在IE8及以下浏览器中，响应函数被触发时浏览器不会传递事件对象，而是将事件对象作为window对象的属性保存的。需要用```var x = window.event.clientX;```的形式调用。

event中的属性```target```表示触发事件的对象，用```event.target.xxx```的形式调用。

当事件的响应函数被触发时，浏览器每次都会将一个事件对象作为实参传递给响应函数。在事件对象中封装了当前事件相关的一切信息，比如鼠标的坐标、键盘哪个案件按下、鼠标滚轮的方向等。

```javascript
areaDiv.onmousemove = function(event){
  /*可以使用这种方法兼容IE
    if(!event){
    event = window.event;
  }*/
  //解决事件对象兼容性问题的方法:利用JS中的或语句，前者为true直接赋值，前者为false返回第二个值。
  event = event || window.event;
  var x = event.clientX;
  var y = event.clientY;
  showMsg.innerHTML = "x=" + x + ", y=" + y;
}
```


### DOM查询的常见方法

+ 元素节点，**通过document对象调用**
方法```getElementById()```、```getElementsByTagName()```、```getElementsByName()```分别是通过id属性、标签名和name属性获取元素节点对象。其中通过id属性一定是获取唯一的对象，其他方法可以获取一组对象。
方法```getElementsByClassName()```会返回一个 **类数组** 对象，返回当前节点的指定class属性值查询一组元素节点对象，但不支持IE8及以下。
方法```getElementsByTagName()```会返回一个 **类数组** 对象，所有查询到的元素都会封装到对象中。主要用来操作表单。（类数组就是HTMLCollection）
方法```getElementsByName()```应该注意，由于```innerHTML```用于获取元素内部的HTML代码，所以对于自结束标签没有意义。

+ 读取元素节点的属性：直接使用```元素.属性名```。但读取```class```属性不能使用这种方式，而应该使用```元素.className```。（class是js中的保留字）

+ 元素节点的子节点，**通过具体的元素节点调用**
方法```getElementsByTagName()```，返回当前节点的指定标签名后代节点。返回一个 **类数组** 对象。
属性```childNodes```，表示当前节点的所有子节点。返回一个 **类数组** 对象。**该属性会获取包括文本节点在内的所有节点。 根据DOM标签，标签间的空白也会被当成文本节点！** 
属性```children```获取当前元素的所有子元素。
属性```firstChild```，表示当前节点的第一个子节点。
属性```firstElementsChild```，获取当前元素的第一个子元素。
属性```lastChild```，表示当前节点的最后一个子节点。

> 注意属性和方法使用方式的不同。

+ 父节点和兄弟节点，**通过具体的节点调用**
属性```parentNode```，表示当前节点的父节点。
属性```previousSibling```，表示当前节点的前一个兄弟节点。如果HTML文档内有换行，也可能获取到空白文本。
属性```previousElementSibling```，获取前一个兄弟元素，IE8以下不支持。
属性```nextSibling```，表示当前节点的后一个兄弟节点。

+ 选择器作为参数
方法```document.querySelector()```需要一个选择器作为参数，可以根据一个CSS选择器来查询一个元素节点对象。使用该方法只能返回唯一的一个元素，如果满足条件的元素有多个，那么它只会返回第一个。
可以使用```document.querySelectorAll()```，它与上面的方法类似，不同的是它会将符合条件的元素封装到一个数组中返回。即使符合条件的元素只有一个也会返回数组。
```html
<body>
  <div class="box1">
    <div>这是box1中的div</div>
  </div>
</body>

<script>
  var div = document.querySelector(".box1 div");
  console.log(div.innerHTML);
</script>
```

+ document中的其它属性
属性```body```，它保存的是```<body>``i`的引用。
属性```html```，它保存的是根标签```<html>``i`的引用。
属性```all```，代表页面中的所有元素。```document.all```和```document.getElementByTagName```含义相同。

### DOM增删改
+ 使用```innerHTML```也可以完成DOM增删改的相关操作，但动作太大了，会把更大部分的HTML代码标记为修改过的状态。所以一般会两种方式结合使用。
+ ```document.createElement()```用于创建一个元素节点对象，需要一个标签名作为参数，根据该标签名创建元素节点对象，并将创建好的对象作为返回值返回。
+ ```document.createTextNode()```用于创建一个文本节点对象，需要一个文本内容作为参数，根据该内容创建文本节点，并将创建好的节点作为返回值返回。
+ ```.appendChild()```用于向一个父节点中添加一个新的子节点。用法：```父节点.appendChild(子节点)```。
+ ```.insertBefore()```用于向指定的 **子节点** 前插入新的子节点，因此调用该方法的一定是**父元素**。用法：```父节点.insertBefore(新的子节点，指定的子节点)```。
+ ```replaceChild()```用于替换**子节点**，因此调用该方法的一定是**父元素**。用法：```父节点.replaceChild(新的子节点，指定的子节点)```。
+ ```removeChild()```用于删除**子节点**，因此调用该方法的一定是**父元素**。用法：```父节点.replaceChild(子节点)```。一种更方便的、不用请它的父元素的方式：```子节点.parentNode.replaceChild(子节点)```。

```javascript
window.onload = function(){
  myClick("mybtn",function(){
    // 创建广州节点<li>广州</li>
    var li = document.createElement("li"); // 创建li元素节点
    var gzText = document.createTextNode("广州"); // 创建广州文本节点
    li.appendChild(gzText);  // 将gzText设置为li的子节点
    var city = document.getElementById("city");  // 将广州添加到city下
    city.appendChild(li);
  })
}
    // 也可以直接利用innerHTML
    var city = document.getELementById(city);
    city.innerHTML += "<li>广州</li>";
    // 两种方法结合【推荐】
    var li = document.createElement("li");
    li.innerHTML = "广州";
    var city = document.getElementById("city");
    city.appendChild(li);
```

### 使用DOM操作CSS
> 能起到修改样式的只有style属性！

+ 通过JS修改元素的样式：```元素.style.样式名 = 样式值```。这里的样式值需要传入字符串，如"200px"。
通过```style```属性设置的样式都是内联样式，而内联样式具有较高的优先级，所以通过JS修改的样式往往会立即显示。但如果在样式中写了```!important```那么它就会有最高优先级，即使通过JS也不能覆盖该样式，会导致JS修改样式失效。所以尽量别为样式添加!important。
> 如果CSS的样式名中含有减号"-"，如background-color，这种名称在JS中不合法。需要将这种样式名修改为驼峰命名法：去掉减号，然后将减号后的字母大写。

+ 通过JS读取元素的样式：```元素.style.样式名```。
> 通过style属性读取到的都是内联样式！无法读取样式表中的样式。

### 读取元素的样式
通过style属性读取到的都是内联样式，无法读取样式表中的样式。

+ （只有IE支持) 获取元素的当前显示的样式：```元素.currentStyle.样式名```。如果当前元素没有设置样式，则获取它的默认样式。
+ 其它浏览器获取元素的当前显示的样式：```元素.getComputedStyle()```。这个方法是window的方法，可以直接使用。需要两个参数，第一个：要获取样式的元素，第二个：可以传递一个伪元素，很少用，一般都传null。该方法会返回一个对象，对象中封装了当前元素对应的样式。可以通过对象.样式名读取样式。
如果获取的样式没有设置，则会获取到真实的值，而不是默认值。比如：没有设置width，它不会获取到auto，而是获取到实际的长度，和显示器大小、人为缩放都有关系。
该方法不支持IE8及以下的浏览器。
通过currentStyle和getComputedStyle()读到的样式都是只读的，不能修改。如果修改必须通过style属性。形如```box1.currentStyle.width = "200px"```的写法是行不通的。

总之，正常浏览器：```getComputedStyle(box1,null).backgroundColor```
IE：如```box1.currentStyle.backgroundColor```

可以定义一个通用的函数，用来获取元素的当前的样式。需要两个参数：obj，要获取样式的元素；name，要获取的样式名。
```javascript
getStyle(box1,"width");

function getStyle(obj,name){
  if(window.getComputedStyle){  // 加上window.，代表是以属性的形式调用。
  // 不加就是变量
  // 变量没有会报错，属性没有会报undefined
    return getComputedStyle(obj,null)[name];  // 正常浏览器的方式。这里注意由于name传入的是变量，所以需要用中括号的语法。  
    return obj.currentStyle.[name]；  // IE8的方式
    
    // 也可以用？ ：表达式整合成一条语句。
  }
}
```

### 其它样式操作的属性
> 这些 **属性** 都是不带px单位的，返回的只是数字，可以直接进行计算

+ 属性```clientX```、```clientY```可以获取鼠标指针**在当前可见窗口**的坐标。
+ 属性```pageX```、```pageY```可以获取鼠标指针**在当前页面**的坐标。
+ 属性```clientWidth```、```clientHeight```：获取元素的**可见**宽度和高度。包括内容区和内边距，不包括边框。该属性是只读的，不能修改，想修改只能用style属性。
+ 属性```offsetWidth```、```offsetHeight```：获取元素的**整个**宽度和高度。包括内容区、内边距和边框。。/÷÷÷÷÷÷÷、
+ 属性```offsetParent```：获取当前元素的最近的开启了定位的父元素。（定位默认static。）如果所有的祖先元素都没开启定位，返回body。
+ 属性```offsetLeft```、```offsetTop```：获取当前元素相对于其**定位父元素**的水平、垂直
+ 属性```scrollHeight```、```scrollWidth```：获取元素的滚动高度/宽度。也就是整个滚动区域的高度/宽度。（通过overflow：auto添加滚动条）
+ 属性```scrollLeft```、```scrollTop```：获取水平/垂直滚动条滚动的距离。
  Chrome认为浏览器的滚动条是body的，可以通过body.scrollTop获取；但事实上，滚动条出现是因为父元素容不下它，所以应该用body的父元素html获取。用```var st = document.body.scrollTop || document.documentElement.scrollTop```解决这个问题。
  
  > 满足scrollHeight - scrollTop ==  clientHeight说明垂直滚动条滚动到底。

## 事件的冒泡、委派、绑定和传播
> 向上冒泡，向下委派。 

###  事件的冒泡
冒泡（bubble）指的就是事件的向上传导，当后代元素的事件被触发时，其祖先元素的 **相同事件** 也会被触发。开发中，大部分情况下的冒泡都是有用的，如果不希望发生事件冒泡可以将事件对象的cancelBubble设置为true。

```javascript
s1.onclick = function(event){
  event = event || window.event;

  // 取消冒泡：可以将事件对象的cancelBubble设置为true。
  event.cancelBubble = true;
}
```

###  事件的委派
场景：希望只绑定一次事件即可应用到多个元素上，即使元素是后添加的。可以尝试将其绑定给元素的共同的祖先元素。

事件的委派：指将事件统一绑定给元素的共同的祖先元素，这样当后代元素上的事件触发时，会一直冒泡到祖先元素。从而通过祖先元素的响应函数来处理。
事件委派利用了冒泡，可以减少事件绑定的次数，提高程序的性能。

###  事件的绑定
使用对象.事件 = 函数的方法绑定响应函数，只能同时为一个元素的一个事件绑定一个响应函数，不能绑定过个，否则后面的会覆盖掉前面的。

通过```addEventListener()```方法也可以为元素绑定响应函数（IE8以上。IE8及以下用```attachEvent()```绑定事件，用法一样，除了第一个参数需要on和执行顺序相反。）。可以同时为一个元素的相同事件同时绑定多个响应函数，这样当事件触发时，响应函数将会按照函数绑定的顺序执行。
参数：
1.事件的字符串，不要on。比如```onclick```在这里写作```click```。
2.回调函数，当事件触发时该函数会被调用。
3.是否在捕获阶段触发事件，需要一个布尔值，一般都传```false```。

此外，```addEventListener()```的this是绑定事件的对象（如下面的btn01），```attachEvent()```中的this则是window。
```javascript
btn01.addEventListener("click",function(){
  alert(1);
},false);
btn01.addEventListener("click",function(){
  alert(2);
},false)
```

为了解决浏览器的兼容性问题，定义一个函数用来为指定元素绑定响应函数。需要注意```addEventListener()```中的this是绑定事件的对象，```attachEvent()```中的this是window，需要统一两个方法中的this。这里使用```call()```方法改变函数执行时的this，这个方法是函数对象的方法，需要通过函数对象来调用，所以我们在它外面套一层匿名函数。实现的效果是一样的，但满足了call的使用条件，再使用call修改this的指向。

```javascript
// 三个参数分别为要绑定事件的对象，事件的字符串和回调函数
// 由于拼串更为容易，所以第二个参数传的是没有on的
function bind(obj,eventStr,callback){
  if(obj.addEventListener){
    // 大部分浏览器兼容的方式
    obj.addEventListener(eventStr,callback,false);
  }else{
    // IE8及以下
    // this是谁由调用方式决定。
    // 原本的调用方式：obj.attachEvent("on" + eventStr,callback)
    obj.attachEvent("on" + eventStr,function(){
      // 在匿名函数中使用回调函数以满足call的适用条件
      callback.call(obj);
    })
  }
}
```

###  事件的传播
关于时间的传播，网景公司和微软公司有不同的理解：微软认为事件应该是由内向外传播，也就是当事件触发时应该先触发当前元素上的事件，然后再向当前元素的祖先元素上传播，也就是时间应该在冒泡阶段执行；网景公司认为事件应该由外向内传播，也就是当事件触发时应该先触发当前元素最外层的祖先元素的事件，然后再向内传播到后代元素，也就是时间应该在捕获阶段执行。

W3C综合了两个公司的方案，将事件传播分成了三个阶段：
1. 捕获阶段：捕获阶段时从最外层的祖先元素向目标元素进行事件的捕获，但是默认捕获阶段内不会触发事件
2. 目标阶段：事件捕获到目标元素，捕获结束开始在目标元素上触发事件
3. 冒泡阶段：事件从目标元素向他的祖先元素传递，依次触发祖先元素上的事件

如果希望在捕获阶段就触发事件，可以将```addEventListener()```的第三个参数设置为true。一般情况下我们不会希望在捕获阶段触发事件，所以这个参数一般都是false。在IE8及以下的浏览器中没有捕获阶段。

方法```setCapture()```：调用一个元素的```setCapture()```方法以后，这个元素将会把下一次所有的鼠标按下相关的事件捕获到自身上。（流氓网站）
方法```releaseCapture()```实现当鼠标松开时，停止对对象的捕获。
这两个方法只有IE支持，火狐中不会报错，chrome会报错。可以用```box1.setCapture && box1.setCapture()```实现浏览器兼容。

## 键盘事件
> 键盘事件通常绑定给可以获取到焦点的对象或document

按键被按下```onkeydown```。如果一直按住某个按键不松手事件会一直触发，为了防止误操作，第一次和第二次间隔会稍微长一点，其它的都很快。
按键被松开```onkeyup```。
【尽量避免使用】可以通过```keyCode```属性获取按键的ASCII编码。```alert(event.keyCode)```。现在是```key```属性。

## 实例：鼠标拖拽

1. 当鼠标在被拖拽元素上按下时，开始拖拽—— ```onmousedown```
2. 当鼠标移动时，被拖拽元素跟随鼠标移动—— ```onmousemove```
3. 当鼠标松开时，被拖拽元素固定在当前位置—— ```onmouseup```

```javascript
var box1 = document,getElementById("box1");
box1.onmousedown = function(){  // 鼠标按下，开始拖拽
  event = event || window.event;
  // div的偏移量：鼠标.clientX - 元素.offsetLeft，Y和top同理
  var ol = event.clientX - box1.offsetLeft;
  var ot = event.clientY - box1.offsetTop;
  
  document.onmousemove = function(event){  // 注意这里是给document绑定
    event = event || window.event;
    var left = event.clientX - ol; 
    var top = event.clientY - ot;
    box1.style.left = left + "px"; // 修改位置。记得开绝对定位。
    box1.style.top = top + "px";
  };

  document.onmouseup = function(){ // 给document绑定。给box1绑定，如果页面有其他元素并且恰好在其他元素的范围内松开鼠标会有bug，因为触发的是其他元素的onmouseup
    // 取消onmousemove的效果。但如果只有这句也有bug，鼠标在页面其他地方点击也会触发
    document.onmousemove = null;
    // 取消onmouseup
    document.onmouseup = null;
  };
  
  // 当拖拽一个网页中的内容时，浏览器会默认去搜索引擎中搜索内容，会导致拖拽功能异常。
  // 这个是浏览器提供的默认行为，如果不希望发生可以通过return false取消默认行为。
};
```

## BOM
浏览器对象模型。BOM可以使我们通过JS操作浏览器，BOM中为我们提供了一组对象用来完成对浏览器的操作。常见的BOM对象如下：

+ Window：代表整个浏览器的窗口，同时```window```也是网页中的全局对象。
+ Navigator：代表当前浏览器的信息，通过该对象可以识别不同的浏览器。
由于历史原因，Navigator对象中的大部分属性都已经不能帮助我们识别浏览器了。一般我们只会用userAgent（这词等价于浏览器）来判断浏览器的信息。userAgent是一个含有用来描述浏览器信息的字符串，不同浏览器有不同的userAgent。
如果userAgent还不能判断，可以通过一些浏览器中特有的对象来判断浏览器的信息。

+ Location：当前浏览器的地址栏信息，通过该对象可以获取地址栏信息或操作浏览器跳转页面。直接打印location则可以获取到地址栏的信息（当前页面的完整路径）。
+ 如果直接将location属性修改为一个完整的路径或相对路径，则页面会自动跳转到该路径，并且会生成相应的历史记录。
方法```assign()```：用来跳转到其他的页面，作用和直接修改location一样
方法```reload()```：重新加载当前页面，作用和刷新一样。如果在方法中传递一个true作为参数，则会强制清空缓存并刷新页面。
方法```replace()```：使用一个新的页面替换当前页面，调用完毕也会跳转页面。但和直接修改location属性不同的是，它不会生成历史记录，不能回退。

+ History：代表浏览器的历史记录，通过该对象不能获取到具体的历史记录，只能操作浏览器向前或向后翻页，而且该操作只在当次访问时有效。
属性```length```：获取到当前访问的链接数量
方法```back()```：回退到上一个页面，作用和浏览器的回退按钮一样
方法```forward()```：回退到下一个页面，作用和浏览器的前进按钮一样
方法```go()```：跳转到指定页面，需要一个整数作为参数——1:向前跳转一个，相当于forward；其它正整数同理。负数就是回退。

+ Screen：代表用户的屏幕信息，通过该对象可以获取到用户显示器的相关信息（主要用在移动端）
  
  > 虽然描述的时候大写，但用起来的时候都是小写

这些BOM对象在浏览器中都是作为window对象的属性保存的，都是全局对象。可以通过window对象来使用，由于是全局对象也可以直接使用。```console.log(window.location)```和```console.log(location)```都行。

## 定时器
+ 方法```setInterval()```：将一个函数每隔一段时间执行一次。
参数——1.回调函数，该函数会每隔一段时间被调用一次；2.调用的间隔时间，单位ms
返回值——一个Number类型的数据，从1开始。这个数字用来作为定时器的唯一标识。
```javascript
setInterval(function(){
  count.innerHTML = num++;
},300)
```
> 很多情况下，要想不出错需要在开启定时器之前将 **当前元素** 上的其它定时器关闭

+ 方法```clearInterval()```用来关闭定时器，传入的参数即是定时器的标识。
参数——它可以接受任意参数，都不会报错。如果参数是一个有效的定时器的标识，则停止对应的定时器；如果参数不是有效的标识就什么都不做。

```javascript
var imgArr = ["1.jpg","2.jpg","3.jpg"];
var index = 0; // 保存当前图片的索引
var timer;  // 保存定时器的标识
var btn01 = document.getElementById("btn01");  // btn01是开始按钮
btn01.onclick = function(){
  // 解决：每点击一次按钮就会开启一个定时器，点击多次就会开启多个定时器，会导致图片切换速度过快，并且由于timer的值会被刷新就只能关闭最后一次开启的定时器的问题
  // 在开启定时器之前将当前元素上的其它定时器关闭
  clearInterval(timer);
  
  timer = setInterval(function(){
    index++;
    // 判断索引是否超过最大索引
    if(index >= imgArr.length){
      index = 0;
    }
    img1.src = imgArr[index];
  },1000);
};
var btn02 = document.getElementById("btn02");  // btn02是结束按钮
btn02.onclick = function(){
  clearInterval(timer);
};
```











