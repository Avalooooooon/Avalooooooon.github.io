---
title: vscode常用快捷键
updated: 2022-01-10 00:26:49
date: 2022-01-10 00:26:49
tags:
categories:
---

+ vscode通用技巧
    1. 自动保存（html即时查看）：左侧栏齿轮-设置-常用设置第一项：autosave或command+F搜索“autodelay”。Auto Save Delay：单位毫秒，值为1000时延迟一秒。
    2. 打开代码中引入的其他文件：command+单击
    3. 同时指定多个光标位置批量操作：alt+左键
    4. 点击左下角设置图标，选择“用户代码片段”可以添加自己的代码段。
    5. 多行增加缩进：选中多行+tab ； 多行减少缩进：选中多行+shift+tab。
+ 整行操作：
    1. 删除一行：shift+command+K 或shift+alt+K
    2. 复制一行：空选状态ctrl+C
    3. 向下开启一个新行而不会在光标所在的位置截断当前行：```command+enter```；向上开启一个新行而不会在光标所在的位置截断当前行：```shift+command+enter```
    3. 移动整行：```alt```加上下方向键
+ html文件操作：
    1. 新建后缀名为html的文件，在文件中输入一个感叹号后直接按tab键可直接一键生成一个最简单的HTML模版
    2. 在html文件里输入```ul>li```后回车，可直接生成一段ul里包含着li、格式正确的表格结构代码；输入```ul+ul```或```ul*2```后回车，可直接生成两个ul结构。也可以嵌套使用，如```ul+li*3```
    3. 在html文件里输入```标签名.classname```后回车，可直接生成一段```class="classname"的<标签>```。如输入```.cla1+.cla2+.cla3```后回车，可直接生成三段```class="classname"的<div>```。
    4. 在html文件里输入```lorem```后回车，可直接生成一段文字内容。
+ Mac特殊字符:shift+command+别的键；option+别的键
+ 11
+ 11

+ 特殊操作
  1. 不能折叠部分代码：在代码段前后分别另起空行并写入```#region```和```endregion```。
  2. 
