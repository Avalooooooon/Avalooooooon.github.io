---
layout: vs
title: gcc配置C++环境（Mingw-w64）
date: 2022-01-06 19:13:53
updated: 2022-01-06 19:13:53
tags: [C++]
categories: [C++]
---


> + [官方文档](https://code.visualstudio.com/docs/cpp/config-mingw#_configure-debug-settings)
> +   [VS Code下 cmake 配置C++环境（windows）](https://blog.csdn.net/qq_44717317/article/details/103658160)

## 下载 Mingw-w64

下载[MinGW-w64 - for 32 and 64 bit Windows](https://sourceforge.net/projects/mingw-w64/files/)。
往下翻一翻，选择最新版本的```x86_64-posix-seh```。

![下载x86_64-posix-seh](download.png)

解压下载下来的压缩包即可。这里我的解压路径为```C: \mingw64```。

## 配置环境变量
进入控制面板->系统和安全->系统->高级系统设置->环境变量，在系统变量的PATH中添加```C:\mingw64\bin```，保存；接下来cmd中以管理员身份运行```gcc -v```，验证是否配置好环境变量。

![验证](yanzheng.png)

## 在VS Code中配置
这一部分最好参考官方文档：[使用Mingw-w64](https://code.visualstudio.com/docs/cpp/config-mingw#_configure-debug-settings)。

### 创建工作区
比如在C盘创建一个空文件夹VSCode-C，然后在该文件夹下面再新建空文件夹“workspace”作为工作区，用于存放各种代码。再在workspace文件夹下创建一个test项目和它的配置文件“.vscode”。

![工作区](workspace.png)

### 安装扩展
![扩展](kuozhan.png)

### C / C ++ IntelliSense配置
按```Ctrl + Shift + P```打开命令面板，键入“ C / C ++”，从建议列表中选择“ 编辑配置（UI）”（Edit Configurations（UI））。VS Code会将在此处进行的更改写入```c_cpp_properties.json.vscode```文件夹中的文件中。主要修改的是配置集、编译器路径和IntelliSense模式。

<img src="peizhi.png" width="70%">C / C ++ IntelliSense配置</img>

写入之后，可以看到 .vscode下添加了```c_cpp_properties.json```文件，然后按照官方给出的配置进行拷贝就可以了。

```json
{
    "configurations": [
        {
            "name": "Win32",
            "includePath": [],
            "defines": [
                "_DEBUG",
                "UNICODE",
                "_UNICODE"
            ],
            "windowsSdkVersion": "10.0.17763.0",
            "compilerPath": "C:/mingw64/bin/g++.exe",//根据个人安装位置修改
            "cStandard": "c17",
            "cppStandard": "c++17",
            "intelliSenseMode": "${default}"
        }
    ],
    "version": 4
}
```

### 创建tasks.json
创建构建任务，即创建一个tasks.json文件来告诉VS Code如何构建（编译）程序。该任务将调用g ++编译器以基于源代码创建可执行文件。

```Ctrl+Shift+P```后输入```tasks```，然后选择“ 任务：配置默认生成任务”（Configure Default Build Task)。在下拉列表中，选择```使用模板创建Tasks.json文件```，然后选择```Others```。VS Code创建一个最小tasks.json文件，并在编辑器中将其打开。
使用以下代码片段替换整个文件内容：

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "build hello world",//这里可以随意命名
      "type": "shell",
      "command": "g++",
      "args": [
        "-g",
        "-o",
        "HelloWorld",   
        "HelloWorld.cpp"
      ],
      "group": {
        "kind": "build",
        "isDefault": true
      }
    }
  ]
}
```

### 配置调试设置
配置VS Code以在按F5键时启动GCC调试器（gdb.exe）。
首次调试.cpp代码时，会弹出选择环境的下拉菜单。选择GDB / LLDB环境。选择“g++.exe-生成和调试活动文件”。
vs code会自动打开launch.json文件。
```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
      {
        "name": "g++.exe - 生成和调试活动文件",
        "type": "cppdbg",
        "request": "launch",
        "program": "C:/VS-Code-C/workspace/HelloWorld/HelloWorld.exe",//按照个人工作区名字配置
        "args": [],
        "stopAtEntry": true,
        //"cwd": "${workspaceFolder}",
        "cwd": "C:/mingw64/bin",
        "environment": [],
        "externalConsole": true,
        "MIMode": "gdb",
        "miDebuggerPath": "C:\\mingw64\\bin\\gdb.exe",//按照个人路径配置
        "setupCommands": [
          {
            "description": "为gdb启用整齐打印",
            "text": "-enable-pretty-printing",
            "ignoreFailures": true
          }
        ],
        "preLaunchTask": "C/C++: g++.exe 生成活动文件"
      }
    ]
  }
```
> VS Code现在已配置为使用Mingw-w64。该配置适用于当前工作空间。要重用配置，只需将三个JSON文件复制到新工作区中的```.vscode```子文件夹，然后根据需要更改源文件和可执行文件的名称。


### 运行HelloWorld.cpp
运行结束后，打开工作区，可以看到新生成了一些文件：

![新工作区](newworkspace.png)

如果你在workspace下再次创建一个Hello文件夹，并在它下面写c++文件，会导致程序无法运行，因为我们配置的工作区为workspace，而在其下面的文件夹里的文件无法生成.exe文件，导致程序 运行报错```[errror]Id returned 1 exit status```。因此我们最好在workspace下直接创建c++文件。
