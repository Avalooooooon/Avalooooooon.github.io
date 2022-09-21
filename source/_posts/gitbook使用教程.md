---
title: gitbook使用教程
tags: [git]
updated: 2021-12-27 12:34:54
date: 2021-12-26 14:39:24
categories: [git]
---



> 参考了[这篇文章](https://segmentfault.com/a/1190000017960359)

## 检查node环境
```bash
node -v
```

## 安装gitbook环境

### 安装gitbook-cli工具
gitbook-cli是一个在同一系统上安装和使用多个版本的GitBook的实用程序。它将自动安装所需版本的GitBook来构建一本书。打开终端输入```npm install gitbook-cli -g```进行全局安装：
```bash
npm install gitbook-cli -g  
gitbook --version
```

> 可能遇到的问题: 
> ```TypeError: cb.apply is not a function```
> 解决方案：降低node版本。10.xx.xx是可行的。

## Gitbook使用
### 初始化一本书（笔记文件夹）
在想要的位置创建一个项目目录，cd到该目录，执行
```bash
gitbook init
```
可以看到该目录下出现了README.md和SUMMARY.md两个文件。

### 编辑目录
如果SUMMARY.md文件有已经编辑好的目录，```gitbook init```时会根据目录内容创建文件。
下面是SUMMARY.md的一个实例：
```markdown
* [Introduction](README.md)
* [第一章](part1/README.md)
    * [1.1](part1/1.md)
    * [1.2](part1/2.md)
        * [1.2.1](part1/2/1.md)
* [第二章](part2/README.md)
    * [2.1](part2/1.md)
* [第三章](part3/README.md)
```
它生成的文件结构如下：
![](wenjianjiegou.png)

### 写笔记
根据喜好选择编辑器即可。

### 启动gitbook服务
```bash
gitbook serve
```
启动服务后，项目文件夹中会生成一个_book文件夹，此文件夹就是最终生成的项目。

### 生成电子书
浏览器打开地址" http://localhost:4000"即可预览。

## 部署到github
首先在项目中创建一个.gitignore文件（和README.md、SUMMARY.md）同级，内容如下：
```gitignore
# 忽略gitbook生成的项目目录
_book
```
此外，由于gitbook生成的项目跟文档的源码是两个部分，所以可以把文档放到master分支上，部署的网站放到gh-pages 分支。

### 在github上创建一个仓库
自行创建即可。

### 本地项目提交到github仓库
```bash
git init
git add .
git commit -m     # 初始化gitbook本地项目'
git remote add origin <git仓库地址>
git push -u origin main # 推送到远程仓库的main分支
```

### 生成项目并上传到github仓库的gh-pages分支
由于打包命令太多，为了简单化，现在写一个脚本命令来自动执行。当然你也可以终端自己执行这些命令。

为了部署方便，可以创建一个脚本文件deploy.sh,内容如下：
```bash
#!/usr/bin/env sh

echo '开始执行命令'
# 生成静态文件
echo '执行命令：gitbook build .'
gitbook build .

# 进入生成的文件夹
echo "执行命令：cd ./_book\n"
cd ./_book

# 初始化一个仓库，仅仅是做了一个初始化的操作，项目里的文件还没有被跟踪
echo "执行命令：git init\n"
git init

# 保存所有的修改
echo "执行命令：git add -A"
git add -A

# 把修改的文件提交
echo "执行命令：commit -m 'deploy'"
git commit -m 'deploy'

# 如果发布到 https://<USERNAME>.github.io/<REPO>
echo "执行命令：git push -f git@github.com:Avalooooooon/ING_leetcode_2201.git head:gh-pages"
git push -f git@github.com:Avalooooooon/ING_leetcode_2201.git head:gh-pages

# 返回到上一次的工作目录
echo "回到刚才工作目录"
cd -
```
注意脚本文件代码中仓库地址要替换成你自己的地址。
文件保存后，在终端执行如下命令，把生成的项目推送到github仓库上的gh-pages分支：
```bash
bash deploy.sh # macos

# windows中，确保已经安装了git工具后直接双击deploy.sh即可运行
```
执行成功后，打开你的github仓库，然后选择branch分支，会发现多了一个gh-pages分支，打开这个分之后，里面会有一个index.html文件。说明部署的代码上传成功了。
注意：如果没有gh-pages分支说明没有部署成功请查看刚才执行的终端看哪里报错了，解决报错直到成功部署。

### 配置GitHub Pages显示网站
在github网站上的仓库里面点击Settings -> GitHub Pages选项中 -> Source里面选择gh-pages branch 然后点击Save按钮，然后在GitHub Pages下面就会看见一个网址，这个网址就是最终的网站。

## gitbook多终端使用
需要一台已经配置好gitbook环境且有完整gitbook文件夹的终端作为条件。
在另一台设备上，git clone仓库的master分支，运行deploy.sh文件。
> 在Windows端，如果出现报错"Error:ENOENT:no such file or directory, stat ‘C:\Users\Lenovo\www\mdspg\md\mybook_book\gitbook\gitbook-plugin-fontsettings\fontsettings.js’"，在C:\Users\username\.gitbook\versions\3.2.3\lib\output\website\copyPluginAssets.js，将```confirm:true```全部替换为```confirm:false```。共两处。

### 多终端发布
不论是在哪台终端，修改了书籍文件后按下列步骤进行操作：
+ ```git pull origin main```，先pull完成本地与远端的融合
+ 修改书籍内容
+ ```git add - A``` ，添加本地所有文件到仓库
+ ```git commit -m 修改说明```，提交修改；
+ ```git push origin main```，更新至main分支；
+ 运行deploy.sh文件，生成项目并上传到github仓库的gh-pages分支；
+ ```gitbook serve```，启动gitbook服务。启动服务后，项目文件夹中会生成一个_book文件夹，此文件夹就是最终生成的项目。

## gitbook的配置文件讲解
如果想对你的网站有更详细的个性化配置或使用插件，那么需要使用配置文件。
配置文件写完后，需要重启服务或者重新打包才能应用配置。
gitbook的配置文件名是book.json，首先在项目的根目录中创建book.json文件。
book.json主要内容：
```json
{
    "title": "我的一本书",
    "author" : "yu",
    "description" : "我第一本书的描述，很好",
    "language" : "zh-hans",
    "structure": {
        "readme": "introduction.md"
    },
    "plugins": [
        "-lunr",
        "-search",
        "search-pro",
        "back-to-top-button"
    ],
    "pluginsConfig": {
        "anchor-navigation-ex": {
            "isShowTocTitleIcon": true
        }
    },
    "links" : {
        "sidebar" : {
            "个性链接1" : "https://www.baidu.com",
            "个性链接2" : "https://www.baidu.com"
        }
    },
    "styles": {
        "website": "styles/website.css",
        "ebook": "styles/ebook.css",
        "pdf": "styles/pdf.css",
        "mobi": "styles/mobi.css",
        "epub": "styles/epub.css"
    }
}
```

### book.json中一些主要参数说明
* title：标题
* author：作者
* description：描述，对应gitbook网站的description
* language：使用的语言，zh-hans是简体中文，会对应到页面的\<html lang="zh-hans" \>
* structure：指定 Readme、Summary、Glossary 和 Languages 对应的文件名，下面是这几个文件对应变量以及默认值：

| Variable | Description     |
| -------- | ---- |
| structure.readme | Readme file name (defaults to README.md) |
| structure.summary | Summary file name (defaults to SUMMARY.md) |
| structure.glossary | Glossary file name (defaults to GLOSSARY.md) |
| structure.languages | Languages file name (defaults to LANGS.md) |


比如想把readme文件换个名字，则可以使用如下配置

```json
"structure": {
    "readme": "introduction.md"
},
```
使用这个配置后，gitbook服务就不会找readme文件，而去找introduction文件当项目说明，这样就可以把readme文件完全当成代码仓库说明文档了。
* plugins：使用的插件列表，所有的插件都在这里写出来，然后使用gitbook install来安装。
* pluginsConfig：插件的配置信息，如果插件需要配置参数，那么在这里填写。
* links：目前可以给侧导航栏添加链接信息
```json
"links" : {
    "sidebar" : {
        "个性链接1" : "https://www.baidu.com"
    }
}
```
* styles：自定义页面样式，各种格式对应各自的css文件
```json
"styles": {
    "website": "styles/website.css",
    "ebook": "styles/ebook.css",
    "pdf": "styles/pdf.css",
    "mobi": "styles/mobi.css",
    "epub": "styles/epub.css"
}
```

## 配置默认主题
默认的主题可以通过配置来做一下效果。
比如侧边栏菜单显示标题数字，可以在配置文件的pluginsConfig参数中写入如下字段：
```json
{
    "pluginsConfig": {
        "theme-default": {
            "showLevel": true
        }
    }
}
```
效果如图：
![](biaotishuzi.png)

5. gitbook的一些实用插件

gitbook插件可以解决一些网站不太方便的地方，如侧边栏导航不能收缩，自带搜索不支持中文等。
用了插件书籍网站会更灵活和美观。

由于插件很多，请参考另一篇文章：<https://segmentfault.com/a/1190000019806829>



