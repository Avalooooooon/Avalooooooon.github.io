---
title: HTML/CSS笔记
updated: 2022-01-09 21:41:50
date: 2022-01-09 21:41:50
tags:
categories:
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

+ 颜色
    1. RGB值：```rgb(红色,绿色,蓝色)```
    2. RGBA值：```rgba(红色,绿色,蓝色,.5)```，第四个是透明度：1为完全不透，0为完全透明，.5为半透明
    3. 16进制值：```#FFFFFF```。两两重复可以简写。
    4. HSL值：（同理有HSLA，色相：0～360+饱和度：颜色浓度0%～100%+亮度：0%～100%+透明度）


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
    + 如果这7个值有一个```auto```，浏览器会自动调             整```auto```。```width```的默认值即为```auto```。
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
    2. 在 ***父元素*** 中垂直且水平居中：```top:0;bottom:0;left: 0；right:0; margin:auto```

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





