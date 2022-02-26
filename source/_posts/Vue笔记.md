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

### 监视
和计算属性语法类似。
```javascript
// 监视属性
watch:{
    istrue:{
      // handler:当istrue发生改变时调用
      handler(newValue,oldValue){ 
        console.log('istrue被修改了',newValue,oldValue)
      }
      // 初始化时让handler调用一下
      immediate:true,
    }
  }
```



### class与style绑定


### 条件渲染


### 列表渲染


### 收集表单数据


### Vue实例生命周期


### 过渡&动画


### 过滤器


### 内置指令与自定义指令


### 自定义插件


## Vue组件化


## 使用Vue-cli

## Vue中的ajax

## Vuex

## Vue-router

## Vue UI组件库
