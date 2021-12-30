---
title: node版本管理（macOS）
tags: [node mac]
updated: 2021-12-26 15:53:53
date: 2021-12-26 15:53:53
categories:
---



## 检查node及npm环境
```bash
node -v
npm -v
```

## 安装node版本管理模块n
```bash
sudo npm i -g n 
n -V
```

## 安装和切换node版本
可能要求权限，在命令前加```sudo```即可。
```bash
# 列出所有node版本
n ls 
# 安装某个版本
n xx.xx.x (版本号)
# 切换node版本(通过上下键盘加回车选择)
n
# 删除某个版本
n rm xx.xx.x
# 使用某个版本来运行脚本
n use xx.xx.x a.js
```