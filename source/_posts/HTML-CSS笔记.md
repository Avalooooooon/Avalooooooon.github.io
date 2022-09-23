---
title: HTML/CSS笔记
updated: 2022-01-09 21:41:50
date: 2022-01-09 21:41:50
tags: [HTML,CSS]
categories: [前端,HTML/CSS]
---

## HTML

> w3school的标签文档：[tags](https://www.w3school.com.cn/tags/html_ref_byfunc.asp)
> MDN的标签文档: [tags](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Attributes)
>
> Vscode安装的扩展：Live Server；
>
> 在浏览器中，检查元素的elements窗口显示的是代码在内存中的结构


### HTML基本结构
> vs code中新建.html文件，输入一个感叹号后直接tab可一键生成一个最简单的HTML模版。

```html
<!DOCTYPE html>
<html lang = "en">
    <head>
        <meta charset="utf-8">
        <title>
        </title> 
        <style>
        /* css中的注释 */
                nav,article,aside{
                    float:left;
                }
                ul{
                    margin:0;
                    list-style:none;
                }
        </style>
        <link rel="stylesheet" href="./css/reset.css">
    </head>

    <body>
      
        <header>
            <span></span>
        </header>
        <main>
            <nav></nav>
            <article></article>
            <aside></aside>
        </main>
        <footer>
            <span></span>
        </footer>
        
        <ul>
            <li>
                <a href="javascript:;">
                </a>
            <li>
            <li>
            <li>
        </ul>
    </body>
    </html>
```
+ ```<DOCTYPE>```:文档声明，用来告诉浏览器当前网页的版本（语法规范）。<!DOCTYPE html>就是html5的文档声明。
+ ```<html>```: html的根标签，除了文档声明，所有内容都要写在根元素内；lang = "en"指定网页语言为英语，会触发浏览器的翻译功能，设置为```zh```则不会触发翻译
+ ```<head>```: 给搜索引擎看帮助其解析网页，其中的内容不会出现在网页中 
+ ```<meta>```: 用来设置网页的元数据（与生俱来的、不会变的、底层的数据），也是给搜索引擎看的；可以通过该标签设置网页字符集```charset```(utf-8)；```name```；```keywords```是网站关键字，给搜索引擎分类网站的；```description```，介绍网站，会显示在搜索引擎搜索的结果中；```title```，作为搜索结果超链接中的文字显示。
+ ```<title>```: 网页标题，在标题栏出现
+ ```<body>```: 需要展示的所有内容都在其中
+ ```<div>```: 没有语义，表示一个区块，是主要的布局元素
+ ```<span>```: 没有语义，表示一个行内元素，是主要的布局元素 


### HTML属性
属性是一个 ***名值对***（x=y，即color="red"），属性值应该使用引号引起来，可以是单引号也可以是双引号，但要一致。
属性用来设置标签中的内容如何显示，

### HTML实体（转义）
如需要在网页中书写特殊符号（多个连续空格、内容两侧的大于号和小于号），需要使用HTML中的实体（转义字符）。语法：```&实体的名字;```。
如，空格：```&nbsp;```，多个连续空格可以写成```&nbsp;&nbsp;&nbsp;```，大于号：```&gt;```，小于号：```&lt;```

> 在[w3school:html实体](https://www.w3school.com.cn/html/html_entities.asp)中查询更多。


## CSS
层叠样式表。声明块中的声明是名值对，名和值用冒号连接。如```color:red```。
注意这里CSS中的名值对用冒号链接；html中的属性值用的则是等号，如```color=red```。

> 样式的继承：为一个元素设置样式的同时也会应用到它的后代元素上。但背景相关的、布局相关的等一些样式不会被继承。

+ 内联（行内）样式：
  
   > 开发时不要使用内联样式！
   
+ 内部样式表：将样式全部写到```<head>```内的```<style>```标签里。
+ 外部样式表：将样式写到外部的```xxx.css```文件中，再通过```<link>```标签引入。

### 浏览器的默认样式
```css
*{
  margin:0;
  padding:0;
}
```
或者：使用重置样式表：style.css直接全部去除了浏览器的默认样式，normalize.css对不同浏览器的默认样式进行了统一。

### 长度和颜色单位
+ 长度
  
    1. 像素（px）：根据屏幕不同而不同，像素越小的屏幕越清晰。同样的200px在不同设备下显示效果不同。
    2. 百分比：是相对其父属性而言。可以使子元素跟随父元素的改变而改变。
    3. em：相对于当前元素（自身）的font- size。1em=1font-size。一般浏览器默认的字体大小都是16px，所以10em就是160px。会根据字体大小的改变而改变。
    4. rem：相对于根元素（\<html\>标签）的font- size。  
    5. vw：视口宽度（viewport width）。

+ 颜色
    1. RGB值：```rgb(红色,绿色,蓝色)```
    2. RGBA值：```rgba(红色,绿色,蓝色,.5)```，第四个是透明度：1为完全不透，0为完全透明，.5为半透明
    3. 16进制值：```#FFFFFF```。两两重复可以简写。
    4. HSL值：（同理有HSLA，色相：0～360+饱和度：颜色浓度0%～100%+亮度：0%～100%+透明度）

+ 像素、（移动端）完美视口
  1. 移动端默认的视口大小是980px(css像素)。所以默认情况下，移动端的像素比就是980/移动端宽度（980/760，iPhone6），也就是一个css像素对应零点几个移动端的物理像素。又因为移动端的物理像素本来就小，所以如果我们直接在网页中编写移动端代码，在980的视口中像素比会很不好，导致网页中的内容很小。这就要求编写移动页面时必须确保有一个比较合理的像素比。
  2. 每一款移动设备设计时都会有一个最佳的像素比，一般把像素比设置为该值就可得到最佳效果。将像素比设置为最佳像素比的视口大小我们称其为完美视口。不同设备完美视口的大小不同：iPhone6-375px，iPhone6 plue-414px。由于不同设备视口和像素比不同，所以同样的375像素在不同设备下意义不同，比如在iPhone6中375是全凭，在plus中就会缺一块。所以在移动端开发时就不能再使用px进行布局了。
  3. 可以通过```<meta>```标签设置视口大小。```<meta name="viewport" content="width=device-width">```就把网页的视口设置为完美视口。总之就是再写移动端的页面时就把这句先写上。

#### 移动端开发解决比例问题的最终方案
使用less+rem的方法，换算设计图px和代码rem的比例。
```less
// total-width是设计图的宽度
@total-width:750; 
html{
  font-size:(100vw/@total-width)*40;
}
.box{
  height:175/40rem; // 这里的175指的是设计图上的宽度为175px
}
```

### 选择器
> 样式冲突：冲突时由选择器的权重（优先级）决定。比较时要将所有选择器的优先级相加计算。但选择器的累加不会超过其最大的数量级，即类选择器最高也不会超过id选择器的优先级。并集（分组）选择器单独计算。
> 
> ***「选择器越具体，优先级越高」***：
> 内联样式 1000 > id选择器 100> 类和伪类选择器 10> 元素选择器 1> 通配选择器 0> 继承的样式 没有优先级；在样式最后加!important直接升为最高优先级。
> 若优先级计算后相等，优先使用靠下的样式。

+ 元素选择器：根据标签名选中指定的元素。语法：```标签名{}```，如```h1{}```。

+ id选择器：根据id选中 **一个** 元素。语法：```#id属性值{}```。

+ 类选择器：根据类名选中 **一组** 元素。语法：```.class属性值{}```。
  
  > 一个标签可以有多个class，class中间用空格分开。如```<p class="cla1 cla2">```。
  
+ 通配选择器：选中页面所有元素。语法：```*{}```。

+ 复合选择器
    1. 交集选择器：```选择器1选择器2选择器n{}```，如```div.cla1{}```。
    
   > 如含有元素选择器，必须使用元素选择器开头。

    2. 并集选择器（分组选择器）:```选择器1,选择器2,...,选择器n{}```。 
  
+ 关系选择器

  父元素（ **直接** 包含子元素）、子元素（ **直接** 被父元素包含）；祖先元素（直接或间接包含后代元素）、后代元素；兄弟元素
    1. 子元素选择器：```父元素 > 子元素{}```。
    2. 后代元素选择器：```祖先 后代{}```
    3. 兄弟元素选择器：
      1. 选择 **下一个** 兄弟，```前一个 + 下一个{}```。
      2.  选择 **下面所有** 兄弟，```前一个 ～ 下一个{}```。
  
+ 属性选择器
    1. 选择含有指定属性的元素：```[属性名]```
    2. 选择含有指定属性和属性值的元素：```[属性名=属性值]```
    3. 选择属性值以指定值开头的元素：```[属性名^=指定值]```
    4. 选择属性值以指定值结尾的元素：```[属性名$=指定值]```
    5. 选择属性值中含有指定值的元素：```[属性名*=指定值]```
    
+ 伪类选择器
  伪类，一般使用:开头，是一种特殊的类，是不存在的类，用于描述一个元素的特殊 ***状态*** 。如第一个子元素、被点击的元素、鼠标移入的元素...
  
    1. 排序伪类
  语法：```:first-child```，第一个子元素；```:nthchild()```，选中第n个子元素，具体n值写在括号内；
  
   > :nthchild()，括号内的特殊值
   > 括号内写n则为全选，2n或even为选中偶数位元素，2n+1或odd为选中奇数位元素；
  
   >> 以上的伪类都是根据所有的子元素进行的排序！想要在同类型元素中进行排序，需要使用形如```:first-of-type```的伪类，用法相似。

    2. 否定伪类
  如```:not()```: 将符合条件的元素从选择器中去除。eg:```.cla1 li:not(:last-child)```。

    3. 超链接伪类
  没访问过的链接：可用```:link```
  访问过的链接：```:visited```。由于隐私原因，只能修改链接颜色
  鼠标状态：```:hover```为鼠标移入的状态。 ```:active```为鼠标点击的状态。

  > 事实上，“ :link ” 可以表示一切正常的链接。

+ 伪元素选择器
  伪元素，一般使用::开头，是一种特殊的元素，是不存在的元素。类似伪类。 如```::first-letter```。<span style="color:red">默认是行内元素！</span>
    + ```::before```、```::after```：必须结合```content```属性使用。
  
  > content中的内容是通过css添加的，是不能选中的！
  
    + 示例：
  ```css
      div::before{
        content:'abc';
        color:red;
      }
```

### 盒模型

>  计算盒子整体 ***可见*** 大小时需要把内容区```content```、内边框```padding```、边框```border```都计算在内！

1. 内容区：```content```
  
  > width和height设置的是内容区（content）的大小。

2. 内边距：```padding```，
  
  > 影响盒大小；背景颜色会延伸到内边距（默认时）。

3. 边框：```border```。
  需要至少设置3个样式——宽度（```border-width```）、颜色（```border- color```）、样式（```border-style```，设置为```none```则无边框）。
  + 简写属性：```border: solid 10px red```，无顺序要求。
  
  > + 关于```border-width```、```border- color```和```border-style```
      1.都可以省略（有默认值）；都有```border-xxx-width/color/style```的形式
      2.四个值：上，右，下，左；三个值：上，左右，下；两个值：上下，左右

4. 外边框：```margin```，不影响盒子***可见框***大小，但影响盒子的实际大小和位置。

> 注意，```margin-top```和```margin-left```移动自己，```margin-bottom```挤别人，```margin-right```经常没用。
> 可以设置为负值。

5. 轮廓：```outline```，用来设置元素的轮廓线，用法和```border```一摸一样。唯一和```border```不一样的点就是轮廓不会影响可见框的大小。
6. 阴影：```box-shadow```，用来设置元素的阴影效果，***不会影响页面布局***。起始位置和元素本身的位置完全重合。
      ```box-shadow: 2px 5px 10px rgba(0,0,0,.3)```

    + 第一个值：水平偏移，正值向右，负值向左。
    + 第二个值：垂直偏移，正值向下，负值向上。
    + 第三个值：模糊半径。即使前两个值都为0，设置了模糊半径也能看到阴影。
    + 第四个值：颜色，常用带透明度的rgba。

7. 圆角：```border-radius```，可以分别指定四个角的圆角。
    + 对某个角同时指定两个半径：椭圆圆角。```border-top-left-radius:10px 20px```
    + 四个值：左上 右上 右下 左下
    + 三个值：左上 右上/左下 右下
    + 两个值：左上/右下 右上/左下
     > 不到四个值，缺哪个找哪个的对角值，这个值代表了两个角的半径。

    + 将元素设置为圆形： ```border-radius:50%```


#### 水平布局
一个元素在其父元素的内容区中存在，该元素的水平布局 ***必须满足***：
> <span style="color:red">左外边距+左边框+左内边距+width+右内边距+右边框+右外边距  =  其父元素内容区的宽度</span>
> 但元素设置浮动```float```后，该等式不需要强制成立！

等式中的宽度```width```，左外边距```margin-left```，左内边距```padding-left```可以设置为```auto```。

+ 如果不满足，如何调整？（注意margin可以为负）
    + 如果这7个值没有```auto```，浏览器会自动调整```margin-right```。
    + 如果这7个值有一个```auto```，浏览器会自动调整```auto```。```width```的默认值即为```auto```。
    + 如果这7个值有两个```auto```，且一个是width一个是margin，则宽度会调整到最大，设置为```auto```的外边距自动为0。
    + 如果三个都是auto，则padding为0，width最大。（全屏）
    + 如果将两个外边距auto，***宽度固定***，则会将外边距设置为相同的值。（元素在父元素居中）
    
      > 经常利用这个特点使元素在子元素内居中

#### 垂直布局
> 默认情况下父元素的***高度***会被内容撑开
> 如果子元素的大小超过了父元素，则子元素会从父元素溢出

使用***overflow***属性设置***父元素***如何处理溢出的子元素，可选值如下：
+ ```visible```：默认值，子元素会从父元素中溢出，在父元素外部的位置显示
+ ```hidden```：溢出内容会被裁剪，不会显示（“一剪没”）
+ ```scroll```：生成垂直和水平两个滚动条，通过滚筒条查看完整内容
+ ```auto```：根据需要生成滚动条（水平还是垂直）
+ ```overflow-x```：单独处理水平方向的
+ ```overflow-y```：单独处理垂直方向的

#### 相邻的垂直方向外边距的折叠（重叠）

> ***相邻*** 且 ***垂直***才适用这种情况。

+ 兄弟元素
    + 两者都是正值：会取两者之间的较大值
    + 特殊情况：一正一负：取两者的和；两负：取两者绝对值较大的
      
      > 兄弟元素外边距的重叠对开发是有利的，不需要处理


+ 父子元素
    + 子元素的会传递给父元素（上外边距）
      > 父子元素外边距的重叠会影响到页面布局，必须处理！
      > 解决方法：要么不用外边距（给父元素padding），要么别相邻（给父元素border，隔开外边距）
    

解决方案：```::before```伪元素 + ```display:table```
```css
.box1::before{
  content:'';
  display:table
}
```
> table既可以解决高度塌陷，又可以解决外边框重叠。


#### 行内元素的盒模型
> 文字的垂直居中：
> <span style="color:red">要让一个文字在父元素中垂直居中，只需让父元素的line-height和父元素height相等。</span>

+ 行内元素不支持设置宽度和高度，因为行内元素没有内容区，宽高是对内容区而言的
+ 行内元素支持设置```padding```、```border```和```margin```，但垂直方向的这些属性不会影响页面的布局
+ ```display```，用来设置元素显示的类型
    + ```inline```：将元素设置为行内元素
    + ```block```：将元素设置为块元素
    + ```inline-block```：将元素设置为行内块元素（既可以设置宽高，又不会独占一行，有点像替换元素的特点）
      
      > 行内块元素同时具备行内元素和块元素的优点和***缺点***。尽量避免使用行内块元素！
    + ```table```：将元素设置为一个表格
    + ```none```：元素不在页面中显示
      
      > 不占据页面的位置！
+ ```visibility```，用来设置元素的显示状态
    + ```visible```：默认值，元素在页面中正常显示
    + ```hidden```：元素在页面中隐藏不显示
      
      > 依然占据页面的位置！

### 浮动
+ <span style="color:red">元素设置浮动后会完全从文档流中脱离，不再占用文档流的位置！</span>脱离后，元素的一些特点也会发生变化。脱离文档流的特点如下：
    1. 块元素不再独占一行，且宽度和高度默认都被内容撑开
    2. ***行内元素会变成块元素。***
    > 脱离文档流后，不需要再去区分块元素和行内元素!

+ 设置浮动后，元素会向其父元素的左侧或右侧移动。默认值为none。
+ 浮动元素不会从父元素中移出。
+ 水平上，浮动元素向左或向右移动时， ***不会超过***  它前面的其他浮动元素。
+ 垂直上，浮动元素不会超过它上边的浮动的兄弟元素，最多和它一样高。
+ 若浮动元素的上边是一个没浮动的块元素，则浮动元素无法上移。
+ 浮动元素不会盖住文字，文字会自动环绕在浮动元素周围。所以可以利用浮动设置文字环绕图片的效果。

#### 浮动的高度塌陷问题
在布局中，父元素的高度默认是被子元素撑开的。但是若子元素浮动后，其会完全脱离文档流，子元素从文档流中脱离将会无法撑起父元素的高度，导致父元素的高度丢失。其下一个元素会自动上移。页面布局混乱。

解决方案：

+ 如果塌陷了的元素样式可以写死，可以直接给塌陷了的元素设置一个height。

+ BFC（Block Formatting Context）、clear和clearfix。

#### BFC
BFC：CSS中的一个隐含属性，可以为一个元素开启BFC。开启BFC后，该元素会变成一个 ***独立的布局区域*** 。元素开启BFC后的特点如下：

+ 开启BFC的元素不会被浮动元素覆盖
+ 开启BFC的元素子元素和父元素的外边距不会重叠
+ 开启BFC的元素可以包含浮动的子元素（解决高度塌陷）
+ 由于BFC是一种隐含属性，不能直接开启，可以通过一些特殊的方式开启元素的BFC：
    + （不推荐）设置元素的浮动（给想要包含浮动子元素的元素开启）。副作用：从文档流中脱离，宽度丢失。可能影响布局。
    + （不推荐）将元素设置为行内块元素。不会从文档流中脱离。副作用：宽度丢失。 
    + （推荐）将元素的overflow设置为一个不是```visible```的属性：<span style="color:red">overflow：hidden或overflow：auto</span>。

#### clear
作用：清除浮动元素对当前元素产生的影响
原理：设置清除浮动后，浏览器会自动为元素添加一个 ***上外边距***，以使其位置不受其他元素的影响。
可选值：```left```：清除左侧浮动元素对当前元素产生的影响；```right```：清除右侧浮动元素对当前元素产生的影响；```both```：清除两侧中最大影响的那侧（最常用）。

> 高度塌陷的最终解决方案：<span style="color:red">用::after伪类</span>
> ```
> .box1::after{
    content:'';
    clear:both;
    display:block/table;
    }
> ```

#### 解决高度塌陷和外边框重叠的最终方案（灵活，推荐）

给存在这两种问题的元素直接添加一个```clearfix```类名：
```css
.clearfix::before,.clearfix::after{
  content:'';
  display:table;
  clear:both;
}
```

### flex布局
主要用来代替浮动完成页面布局。可以使元素带有弹性，让元素可以跟随页面大小的改变而改变。
当父元素盒子设置为flex布局后，子元素的float、clear、vertical-align属性将失效。
弹性容器：通过```display:flex```设置为块级弹性容器，```display:inline-flex```设置为行内的弹性容器。
弹性元素：弹性容器的 ***直接子元素*** 是弹性元素。
元素可以既是弹性容器，又是弹性元素。

#### 弹性容器的样式
主轴：弹性元素的排列方向；侧轴：与主轴垂直方向。凡是```justify***```都是主轴的，```align***```都是辅轴的。
1. 容器中弹性元素的排列方式：```flex-direction```
2. 弹性元素是否在容器中自动换行：```flex-wrap```
3. 排列方式和换行的简写属性：```flex-flow```

对齐相关的样式：
1. ```justify-content```：元素在主轴如何对齐（如何分配主轴空白空间）
  + ```flex-start```、```flex-end```：沿主轴起边/终边对齐
  + ```center```：居中
  + ```space-around```、```space-between```、```space-evenly```
2. ```align-content```：设置元素在辅轴如何对齐（如何分配辅轴空白空间）。可选值和```justify-content```一样，多一个```stretch```。
  
  > 只能用于子项出现***换行***的情况（多行），单行下没有效果。
  
3. ```align-items```：子项为单行时使用。设置元素在辅轴如何对齐。
  > 适用于单行的情况，只有上对齐、下对齐、居中和拉伸。
  
  + ```stretch```：默认值，将元素的长度设置为相同的值
  + ```flex-start```、```flex-end```：沿辅轴起边/终边对齐，不会拉伸元素
  + ```center```：居中
  + ```base-line```：基线对齐，用的不多。

#### 弹性元素的样式
只有三个：增长、缩短、基础长度。
通过```align-self```覆盖当前元素上的```align-items```。
1. ```flex-grow```、```flex-shrink```：指定弹性元素的伸展/收缩系数。父元素的剩余空间会按照比例分配/当父元素空间不足以容纳所有子元素时，对子元素进行压缩。
2. ```flex-basis```：元素在主轴上的基础长度。默认auto，即参考元素自身的高度和宽度。
> 这三个值可以看作是弹簧的三个状态：伸长，压缩，放松。

可以通过简写属性```flex```设置弹性元素所有的三个样式。```flex:增长 缩减 基础```。
1. ```initial```：即为```flex:0 1 auto```，只能减。
2. ```auto```：即为```flex:1 1 auto```，可增可减。
3. ```none```：即为```flex:0 0 auto```，元素没有弹性。

还可以使用```order```决定弹性元素的排列顺序。

### grid布局
与flex最大的不同就是，Flex是轴线布局，指定的都是针对轴线的位置，可以看作是一维布局；而Grid 布局则是将容器划分成行列，产生单元格，然后指定"项目所在”的单元格，可以看作是二维布局。通过```dispaly:grid;```（```dispaly:inline-grid;```）开启。
> 虽然grid布局很强大，但兼容性差于flex。使用前应注意兼容性问题。

#### 容器属性
行高和列宽：
1.```grid-template-columns```：每一列的列宽，如```grid-template-columns:100px 100px 100px```，即显示为三列，每一列宽度100px 。和```grid-template-columns:repeat(3,100px)```效果相同。
  +  ```auto-fill```：当单元格大小固定，但容器大小不确定时，使用```auto-fill```属性进行自动填充。如```grid-template-columns: repeat(auto-fill,100px)```。
  + ```fr```：为了方便表示比例关系，网格布局提供了fr关键字(fraction 的缩写，意为"片段”)。如```grid-template-columns:repeat(4,1fr)```，意为列宽平均分成四等份。```grid-template-columns:1fr 2fr 3fr```，意为列宽分成6份，各占 1 2 3 份。
  + ```minmax()```：函数产生一个长度范围，表示长度就在这个范围之中，它接受两个参数，分别为最小值和最大值。第一个参数最小值,第二个最大值。
  + ```auto```：表示由浏览器自己决定长度。
  +  **网格线**：可以用方括号定义网格线名称，方便以后给盒子定位使用。如```grid-template-columns: [c1] 100px [c2] 100px [c3] 100px [c4]```。

2.```grid-template-rows```：每一行的行高。

间距：item相互之间的距离。
1.```row-gap```：一行中相邻item的间距。
2.```column-gap```：一列中相邻item的间距。
3.```gap```：前二者的简写属性。

设置区域内单元格是否使用：```grid-template-areas```。用法如下图，名字相同标识在同一个区域，定位或者写项目属性的时候会用到。
![单元格使用方式](grid-template-areas.png)

设置子元素的排放顺序：
划分网格后，容器的子元素会按照顺序自动放置在每一个网格。默认的放置顺序是"先行后列"，即先填满第一行，再开始放入第二行。
1.```grid-auto-flow:row```：同默认放置方式。
2.```grid-auto-flow:column```：先放入第一列，满了再开始放入第二列。
3.```grid-auto-flow:row dense```：表示换行时留下的空间可以由下面的元素填补上去。
![row dense](row dense›.png)

设置对齐相关的样式：
1.```justify-items```：单元格内容的水平对齐方式。可选：start｜end｜center｜stretch。
2.```align-items```：单元格内容的垂直对齐方式。可选同上。
  > ```place-items : start end```是这两个属性的简写。

3.```justify-content```：整个内容区域（所有的项目的总和）的水平对齐方式。可选：start｜end｜center｜stretch｜space-around｜space-between｜space-evenly。
4.```align-content```：整个内容区域（所有的项目的总和）的垂直对齐方式。可选同上。

设置多出来的项目的宽高：
```grid-auto-columns / grid-auto-rows```。

#### 项目属性
根据在哪根网格线设置item的具体位置：
使用```grid-column-start / grid-column-end grid-row-start / grid-row-end```。
![设置item位置](item1.png)
```grid-column: 1 / 3```是前两个的简写。
![设置item位置](item2.png)
指定了项目所占的格数，跨列数：
![设置item位置](item3.png)

设置项目放在哪一个区域：
通过```grid-area```配合```grid-template-areas```设置。```grid-area```和``` grid-row-start / grid-column-start / grid-row-end / grid-column-end```的缩写格式为：```grid-area:1 / 1 / 2 / 3```。
![根据area设置](area1.png)

设置对齐相关的样式：
通过```justify-self / align-self / place-self```。
![设置对齐](duiqi1.png)
![设置对齐](duiqi2.png)


### 定位
用```position```属性。默认为```static```，即元素是静止的，没有开启定位。只要开了定位，就都是一个层级；和哪种定位无关。

> 关于层级：
> 如果元素的层级一样，则优先显示靠下的元素。
> 祖先元素的层级再高也不会盖住后代元素。

#### 绝对定位：absolute
<span style="color:red">相对于其包含块（containing block）定位。</span>
> 关于包含块（正常情况下，包含块就是当前元素 **最近** 的祖先 **块** 元素）
> 绝对定位的包含块：当前元素最近的开启了定位的祖先元素；若所有祖先元素都没开启定位，则根元素就是它的包含块。

+ 若不设置偏移量，***元素位置*** 不会发生变化。
+ 开启绝对定位的元素会 **从文档流中脱离** ，性质发生变化：行内变成块，块的宽高被内容撑开。
+ 会使元素提升一个层级。


+ 水平方向的布局等式需要添加left和right两个值。规则还和以前一样。
+ 当发生过度约束时：
    1. 如果9个值中没auto，则自动调整right；
    2. 如果有auto（margin width left right），则自动调整auto的值：
  由于left和right的值默认是auto，所以当未设置它们的值且等式不满足时会自动调整这两个值。所以如果想通过margin设置居中的话必须要设置left和right为0。
  即水平居中：```left: 0；right:0; margin-left:auto;margin-right:auto```
+ 绝对定位后，垂直方向的等式也必须满足：top+margin-top/bottom+padding-top/bottom+height=包含块的高度
    1. 垂直居中：```top:0;bottom:0;margin-top:0;margin-bottom:0```
    2. 在 ***父元素*** 中垂直且水平居中：```top:0;bottom:0;left:0;right:0; margin:auto```

#### 相对定位：relative
“灵魂出窍：移出来的是魂，肉体还在之前的位置”
偏移量：```top```,```bottom```,```left```,```right```，是定位元素和定位位置上下左右的距离。
<span style="color:red">参照  ***元素自身在文档流***  中的位置。而且， ***没有脱离文档流***。</span>
相对定位的特点：

+ 开启后，若不设置偏移量，元素不会发生任何变化
+ 会提升元素的层级
+ 不会改变元素性质：块还是块，行内还是行内

#### 固定定位：fixed
也是一种绝对定位，大部分特点和绝对定位一样。
唯一不同：固定定位永远参照浏览器的视口进行定位。不会随网页的滚动条滚动。

#### 粘滞定位：sticky
兼容性较差。一般不采用。（完全不兼容IE）
+ 和相对定位的特点基本一致
+ 不同的是粘滞定位可以在元素到达某个位置时进行固定，参照包含块。

### 文字
font可以设置字体相关的所有属性。字体大小和字体族必须有。会覆盖单独设置的值。
```css
font: 样式（font-style） 字重（font-weight） 字体大小/行高（lineheight） 字体族
font：italic bold 50px/2 微软雅黑，‘Times New Roman’,Times,serif; 
```
#### 行高lineheight和字体框

行高```lineheight```指的是文字占有的实际高度。可以任意指定一个大小（px em），也可以设置为整数，整数的意义是字体的指定的倍数。
> 注意行高指的是 ***单行*** 的行高！

字体框是字体存在的格子，设置font-size字体大小实际就是设置字体框的高度。

行高会在字体框上下平均分配。

+ 可以将行高设置为和高度一样的值，使 ***单行*** 文字在父元素中垂直居中
+ 行高经常还用来设置文字的行间距。css没有行间距的属性，利用公式```行间距=行高-字体大小```来间接控制行间距。

#### 文本的水平和垂直对齐
水平对齐：```text-align```，默认值left ；right ；居中对齐center ；两端对齐justify
> 注意```text-align```只能给块元素设置！

垂直对齐：```vertical-align```，默认值基线对齐baseline；顶部对齐top；底部对齐bottom；居中对齐middle（中线对齐）。还可以直接指定值```vertical-align:100px```。

> 用img标签引入图片时，由于图片作为替换元素性质很像行内元素，默认是基线对齐，导致图片和它父元素的border之间有一条缝隙，这个缝隙就是图片的 ***基线*** 。
> 想消除这个缝隙：将图片的vertical-align设置为除baseline外的任意值即可。

#### 其他文本样式
设置文本修饰：```text-decoration```。不兼容IE。
可选：none ；overline ；underline ；line-through。

设置网页如何处理空白：```white-space```。
可选：normal ；nowrap ；pre保留空白（预处理文本，保留html文件中写的格式）；

> 如何实现文字显示不全，有省略号的效果？
> ```css
> .box{
>   width:200px;
>   white-space:nowrap;
>   overflow:hidden;
>   text-decoration:ellipsis;
> }
> ```
>
> 其中width设置文本包含框宽度；```white-space:nowrap; overflow:hidden; text-decoration:ellipsis;```三者缺一不可。

### 背景
可以使用简写属性```background```设置。

+ background- size必须写在background-position后面，并且使用/隔开。即```background-position/background-size```。
+ background-origin必须在background-clip前面。

```css
background: url("./1.png") #bfa center center/contain no-repeat；
```

同时设置背景图片和背景颜色：```background-img:url("./1.png")```。
背景重复方式：```background-repeat:no-repeat```。
背景图片的位置：```background-position:top left```。使用方位词时必须同时指定两个，如果只写一个另一个默认为center。也可以通过偏移量指定背景图片的位置。
背景图的偏移量：```background-origin:content-boc```。
背景图片大小：```background-size：100% auto```。第一个值宽度，第二个值高度，如果只写一个则另一个默认为auto（图片比例不变）。
cover：图片比例不变，将元素铺满
contain：图片比例不变，将图片在元素中完整显示

#### 关于background-position的计算原点
padding-box：默认值，background-position从内边距（左上角）开始计算
content-box：从内容区处计算
border-box：从边框处计算
用```background-clip:padding-box```设置背景范围。
border-box：默认值，背景会出现在边框的下面（内容区内边框和边框）
padding-box：背景只到内边距（内容区和内边距出现），不会出现在边框
content-box：背景只到内容区

用```background-attachment:scroll```设置背景图片是否跟随元素移动。
scroll：默认值，背景图片会跟随元素移动
fixed：背景会固定在页面中

#### 渐变色

> 需要通过backgroud-image设置！

+ 线性渐变：```backgroud-image:linear-gradient(to right,red,yellow,#bfa)```。开头可以指定渐变方向，多个颜色默认平均分布。
    + 可以平铺的线性渐变：```backgroud-image:repeating-linear-gradient(to right,red,yellow,#bfa)```
+ 径向渐变（放射渐变）：```backgroud-image:radial-gradient(100px 100px at top left,red,yellow)```
    + 语法：```radial-gradient(大小at位置,颜色 位置,颜色 位置,...)```
    + 大小：circle、ellipse、closet/fasthest-side/corner
    + 位置：top、right、left、center、bottom

### 表格
table中，使用tr表示表格的一行，有几个\<tr\>就有几行。
tr中，使用td表示一个单元格，有几个\<td\>就有几个单元格。
用```rowspan```指定行宽。即纵向合并单元格。
用```colspan```指定列宽。

下面是一个两行四列的表格。
```html
    <table border="1" width="50%" align="center">
        <tr>
            <td colspan="2">a1</td>
            <td>c1</td>
            <td rowspan="2">d1</td>
        </tr>
        <tr>
            <td>a2</td>
            <td>b2</td>
            <td>c2</td>
        </tr>
    </table>
```

可以将一个表格分成三部分：头部```thead```，主体```tbody```，底部```tfoot```。还有\<th\>标签，表示头部部分的单元格，会有加粗效果。

> 若表格中没有使用```tbody```而是直接使用```tr```，那么浏览器会自动创建一个```tbody```，并且将```tr```全部放入```tbody```。因此 ***tr不是table的子元素*** ！

#### 表格样式
指定边框之间的距离：```border-spacing```
设置边框的合并：```border-collapse:collapse```
设置隔行变色效果：```tr:nth-child(odd){backgroundcolor:gray;}```

默认情况下元素在td中垂直居中。也可以通过```vertical-align```设置垂直对齐方式。用```text-align```设置水平对齐方式。（top bottom center）

> 利用这个特性，可以通过将父元素```display:table```和```vertical-align```让子元素垂直居中。注意水平居中仍需要用```margin:0 auto```实现，不能直接使用```text-align```。

### 表单
> 数据要提交到服务器中，必须要为元素指定一个name属性！


```html
<form action="target.html">
	<input type="text" name="username" value="这是一个文本框" autocomplete="off">
	<input type="password" name="password" value="这是一个密码框" disabled>
	<br><br>
	<input type="submit" value="注册" value="这是一个提交按钮" readonly>
</form>
```

必填项```action```：内容为表单要提交的服务器的地址。
属性```autocomplete```：是否开启自动补全。

单选框：同一个“name”字段即可划分到同一组单选按钮中，“value”属性会作为用户填写的值发给服务器。“checked”可以将单选按钮设置为默认选中。
```html
<input type="radio" name="group1" value="a">
<input type="radio" name="group1" value="b" checked>
```

还有多选框（type="checkbox"）、下拉列表（\<select\>\<option\>)等。

### 过渡
简写属性：```transtion```。只有一个要求：如果要写 ***延迟*** ，则两个时间中的第一个是持续时间。
可以指定一个属性发生变化时的切换方式。
> 注意过渡时必须有两个有效数值！在它们之间过渡。auto不能起作用。

+ 要执行过渡的属性：```transtion-property:width```。多个属性间使用，隔开，若所有属性都需要过渡，用```all```关键字。
+ 过渡效果持续时间：```transtion-duration```。
+ 过渡的时序函数：```transtion-timing-function```。
  + 默认为```ease```，慢速开始，先加速再减速。
  + 其他可选值：```linear```,```ease-in```,```ease-in-out```。用```cubic-bezier()```自己指定时序函数，参考[本链接](https://cubic-bezier.com)。
  + 用```steps()```分布执行过渡效果。可以设置一个第二个值：```end```，在时间结束时执行过渡（默认值）; ```start```，在时间开始时执行过渡。
+ 过渡效果的延迟：```transtion-delay```，等待一段时间后再执行过渡。

### 动画
简写属性：```animation```。只有一个要求：如果要写 ***延迟*** ，则两个时间中的第一个是持续时间。
过渡需要在某个属性发生变化时才会触发。动画可以自动触发。设置动画效果，必须先设置一个 ***关键帧*** ，关键帧设置了动画执行的每一个步骤。

+ 要对当前元素生效的关键帧的名字：```animation-name:test```
+ 动画持续时间、延时和时序函数类似过渡：```animation-duration animation-delay animation-timing-function```
+ 动画执行次数：```animation-iteration-count```
+ 动画运动方向：```animation-direction```。可选：```normal reverse alternate alternate-reverse```。
+ 动画执行状态：```animation-play-state```。可选：```running paused```
+ 动画填充模式：```animation-fill-mode```。可选：```none```，默认值，动画执行完毕元素回到原来位置；``` forwards```，动画执行完毕元素停在结束位置；```backwards```，动画延时等待时元素处于开始状态；```both```，结合了forwards和backwards。


#### 关键帧
```css
@keyframes test{
  /* from表示动画的开始位置，也可以使用0% */
  from{
    margin-top: 0;
  }
  25%,to{
    margin-top: 400px;
    animation-timing-function: ease-out;
  }
  50%{
    margin-top: 100px;
  }
  /* to表示动画的结束位置，也可以使用100% */
```

### 变形
通过CSS改变元素的形状或位置。不会影响到页面布局。
变形：```transform```。
指定变形原点：```transform-origin：0 0 ```

#### 平移
使用```translateX(100%)```等。百分比是相对于 ***自身*** 去算的。
之前所说的垂直且水平居中的方法：```top:0;bottom:0;left:0;right:0; margin:auto```，***只适用于元素大小确定的情况；若元素大小是被内容撑开的则不能使用！***因为在width、height、margin均为auto的情况下会优先调整宽和高。

+ 利用X、Y轴方向上的平移实现水平/垂直居中，适用于元素大小被内容撑开的情况：
水平居中：```left:50%; transform:translateX(-50%);```
水平且垂直居中：```left:50%; top:50%; transform:translateX(-50%) translateY(-50%);```。

+ Z轴平移：立体效果（近大远小）。默认情况下网页不支持透视。如果需要看见效果，必须设置网页视距如：```perspective：800px```。一般在html或body标签的样式中设置。

#### 旋转
使用```rotateX()```使元素沿着x(y z)轴旋转指定的角度。
是否显示元素背面：```backface-visibility：hidden```

#### 缩放
使用```scaleX()```等实现各个方向的缩放。

## less
事实上，css原生也支持变量的设置和计算函数calc()，但兼容性欠佳。

```css
html{
  --color:#bfa;
}
.box{
  width:calc(400x/2);
  color:var(--color);
}
```

+ less是一门css的预处理语言。它是css的增强版，通过less可以编写更少的代码实现更强大的样式。在less中添加了很多新特性，如对变量的支持，对mixin的支持，在less中所有的数值都可以直接进行运算…
> 新版less中，除法运算必须带着单位放在括号内！

+ 语法大体上和css一致，但增加了许多对css的扩展。所以浏览器无法直接执行less代码，要执行必须将less转化为css，然后再由浏览器执行。在vscode中，需要安装插件：easy less。
+ 用```//```对less进行单行注释，这种注释方法中的内容不会被解析到css中。用```/* */```也可以对less进行注释，内容会被解析到css文件中。
+ 可以通过在less文件开头```@import "demo.less"```引入其他外部less文件，方便模块化开发。

### 语法
变量：语法为```@变量名```。变量发生重名时优先使用比较近的变量。
```less
@a:200px;
@b:box2;

// 作为类名或一部分值使用时必须以 @{变量名} 的形式使用
.@{b}{
  // 直接使用以 @变量名 的形式使用
  width:@a;
}

div{
  width:300px;
  height:$width;
}
```

父元素：
```less
.box1{
	>.box3{
		color:blue;
		// & 表示的是外层的父元素box3
		&:hover{
			color:yellow;
		}
	}
	
	// & 表示的是外层的父元素box1。                        
	div &{
		width:300px;
	}
}
```

扩展：```:extend()```。对当前选择器扩展指定选择器的样式（选择器分组）
```less
.p1{
	width:100px;
	height:200px;
}
.p2:extend(.p1){
	color:red;
}
```
功能类似的还有混合函数。
```less
// 也可以直接引用指定的样式，相当于将p1的样式在这复制。
// mixin混合
.p3{
	.p1();
}

// 使用类选择器时可以在选择器后边添加括号，这就创建了一个mixins。再引用的时候可以省略括号。
.p4(){
	width:100px;
}
.p5{
	.p4;
}

// 在混合函数中可以直接设置变量，也可以给变量设置默认值。设置默认值再引用可以不用全指定值。
.test(@w,@h,@bg-color){
	width:@w;
	height:@h;
	border:1px solid @bg-color;
}
div{
	// 调用混合函数，按顺序或按名字传递参数
	.test(200px,300px,#red)
	// 或
	.test(@bg-color:red,@h:100px;@w:300px)
}
```

## 媒体查询
语法：```@media 查询规则{}```。

+ 媒体类型
  1. all：所有设备
  2. print：打印设备
  3. screen：带屏幕的设备
  4. speech：屏幕阅读器

+ 媒体特性
  1. width、height：视口宽度、视口高度
  2. min-width、max-width：视口大于/小于指定宽度时生效

样式切换的分界点称为断点，也就是网页样式会在这个点发生变化。

+ 常用断点
  1. 小于768：超小屏幕，max-width=768px
  2. 大于768：小屏幕，min-width=768px
  3. 大于992：中型屏幕，min-width=992px
  4. 大于1200:大屏幕，min-width=1200px

可以在媒体类型前添加```only```，表示只有。```only```的使用主要是为了兼容一些老版本浏览器。
使用逗号连接多个媒体类型，表示它们之间是 ***或*** 的关系。如```@media print,screen{}```；使用```and```连接多个媒体类型，表示它们之间是 ***且*** 的关系。在选择器前加```not```，表示“除了”，如```@media not only screen and (min-width:768px){}```。一个较为完整的写法如下：

```less
@media only screen and (min-width:768px) and (max-width:992px){
  body{
    background-color: #red;
  }
}
// 或
@media only screen {
  @media (min-width:768px) and (max-width:992px){ // 注意media后面要空格
    body{
      background-color: #red;
    }
  }
}
```