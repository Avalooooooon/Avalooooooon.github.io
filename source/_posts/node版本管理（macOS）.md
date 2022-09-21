---
title: node版本管理（macOS和windows）
tags: [node]
updated: 2021-12-26 15:53:53
date: 2021-12-26 15:53:53
categories: [前端,JS]
---

>  安装的npm全局模块不会在各个版本的node.js之间共享。还有可能有些npm包不支持当前使用的node版本 
>
>  不要使用单数的node版本！


## macOS：使用 n 版本管理工具
### 检查node及npm环境
```bash
node -v
npm -v
```

### 安装node版本管理模块n
```bash
sudo npm i -g n 
n -V
```

### 安装和切换node版本
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

## Windows/macOS：使用 nvs 版本管理工具
### 安装nvs
> nvs的设置参考[官方说明](https://github.com/jasongin/nvs/blob/master/doc/SETUP.md#manual-setup---command-prompt)

在[nvs/releases](https://github.com/jasongin/nvs/releases) 下载最新版本的 nvs.msi，双击安装。
添加环境变量：
```bash
NVS_HOME=%LOCALAPPDATA%\nvs
```
下面是一些基本命令。
```bash
# 初始化并使用 NVS
nvs install	

# 从 profile 和 environment 中移除 NVS
nvs uninstall	

# 展示 NVS 版本
nvs --version	
```

如果想要把lts设置为系统默认的node版本：
```bash
nvs link lts
```
注意，nvs use 是改变当前shell窗口的node版本，不是全局环境（nvm的区别）；nvs link是改变全局环境的node版本。

### 配置镜像地址
国内需要把对应的镜像地址修改为淘宝的镜像地址。
```bash
nvs remote node https://npm.taobao.org/mirrors/node/
nvs remote
```
> nvs remote命令允许配置多个命名的下载位置。NVS 分别管理来自不同远程位置的版本，因此没有版本冲突的风险。默认情况下，只有一个远程指向 Node.js 官方版本： 
> ```bash
> $ nvs remote
> default  node
> node     https://npm.taobao.org/mirrors/node/
> ```
> 这样就可以从其他来源获得构建。以下命令为 nightly 添加了一个远程 remote，列出了 nightly ，并添加了一个构建：
>  ```bash
>  $ nvs remote add nightly https://nodejs.org/download/nightly/
>  $ nvs lsr nightly/13
>  nightly/13.1.1-nightly20191120c7c566023f
>  ...
>  $ nvs add nightly/13
>  ```

### 安装和切换node版本
```bash
# 展示本地的Node.js列表
nvs ls [filter]	

# 切换Node版本
nvs use node/[version]/x64

# 更新当前环境的 Node.js 至最新版本
nvs upgrade [fromver]

# 列出可下载的 Node.js 版本
nvs ls-remote [filter]
nvs lsr [filter]

# 下载某个版本的 Node.js
nvs add [version]	

# 移除某个版本的 Node.js
nvs rm <version>	

```

### 根据目录自动切换版本
在 Bash 或 PowerShell 中，NVS 可以在更改目录时自动切换当前 Shell 中的 Node.js 版本。默认情况下，此功能处于禁用状态。使它运行nvs auto on。之后，无论何时cd或pushd在包含.node-version或.nvmrc文件的目录下，NVS 都会相应地自动切换 Node.js 版本，并在必要时下载新版本。当您cd到达目录上方没有目录.node-version或.nvmrc文件的目录时，将还原默认（链接）版本（如果有）。
> Windows 命令提示符中不提供此功能。请用 PowerShell。

```bash
~$ nvs link 6.9.1
~/.nvs/default -> ~/.nvs/node/6.9.1/x64
~$ nvs use
PATH += ~/.nvs/default/bin
~$ nvs auto on
~$ cd myproject
PATH -= ~/.nvs/default/bin
PATH += ~/.nvs/node/4.6.1/x64/bin
~/myproject$ cd ..
PATH -= ~/.nvs/node/4.6.1/x64/bin
PATH += ~/.nvs/default/bin
```

如果您的外壳与自动切换不兼容，或者您 ​​ 希望手动切换但仍利用其中的任何一个```.node-versionor.nvmrc```文件，则可以```nvs use auto```使用该版本运行，也可以直接运行```nvs auto```。
```bash
nvs use auto 或 nvs auto
```

### VS Code 支持nvs
>这部分参考[官方说明](https://gitee.com/wsz7777/nvs/blob/gitee/doc/VSCODE.md)

VS Code可以使用 NVS 选择启动或调试时要使用的 Node.js 版本。在launch.json（位于项目根目录的.vscode文件夹中）添加一个“runtimeArgs”属性，该属性包含一个NVS可识别的版本字符串，以及一个“runtimeExecutable”属性，该属性指向nvs.cmd（Windows）或nvs（Mac, Linux）。 
对于多平台开发，可以为每个平台自定义配置。如果 NVS 不在 VS Code 的 PATH 环境变量中，则可能需要指定一个绝对路径，例如"${env:HOME}/.nvs/nvs")
配置示例：配置launch.json，以便VS Code使用NVS启动节点版本6.10：
```json
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Launch Program",
      "program": "${file}",
      "args": [ ],
      "runtimeArgs": [ "6.10" ],
      "windows": { "runtimeExecutable": "nvs.cmd" },
      "osx": { "runtimeExecutable": "nvs" },
      "linux": { "runtimeExecutable": "nvs" }
    },
  ]
```

"runtimeArgs"中的NVS版本字符串值由完整或部分语义版本号或版本标签（“lts”、“latest”、“Argon”等）组成，可选前跟远程名称，可选后跟处理器架构或位数（“x86”、“x64”、“32”、“64”），用斜杠分隔。当部分版本与多个可用版本匹配时，会自动选择最新版本。示例：“节点/lts”、“4.6.0”、“6/x86”、“节点/6.7/x64”。NVS别名也可以代替版本字符串。

或者"runtimeArgs"中的版本字符串可能会被省略（相当于“auto”），在这种情况下，NVS搜索项目目录或父目录中最近的.node-version文件。如果找到，则下载（如有必要）并启动文件中指定的版本。如果没有找到.node-version文件，则将启动默认（链接）版本（如果有的话）。

当不将自动模式与.node-version文件一起使用时，launch.json中指定的节点版本必须已经使用nvs add命令下载。否则，启动将失败，NVS打印错误信息“未找到指定版本”。 

有关更多详细信息，请参见[NVS VS Code文档](https://gitee.com/wsz7777/nvs/blob/gitee/doc/VSCODE.md)。或者执行命令```nvs help vscode```。

## Windows：使用 nvm 版本管理工具
### 查看nvm版本
```bash
nvm version
```

### 安装和切换node版本
```bash
# 列出所有node版本。* 代表当前版本。末尾输入 available 显示可供下载的所有版本列表。
nvm list [available]

# 安装某个版本。version：版本号 例如 8.9.0 或者 latest（最新稳定版）,[arch]可选、指定是否安装32位或64位版本（默认为系统架构），将[arch]设置为 all 安装32和64位版本。
nvm install <version> [arch]

# 切换node版本。可选[arch]32和64位版本
nvm use <version> [arch]

# 卸载指定的node版本
nvm uninstall <version>

# 设置nvm存放不同版本的node.js的目录。 如果没有设置 path ，则显示当前的根目录
nvm root <path>

# 设置node是以32还是64位模式运行。 指定32或64来覆盖默认操作系统版本。
nvm arch [32|64]
```