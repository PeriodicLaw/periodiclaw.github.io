---
layout: post
title: 配置VSCode中遇到的一些坑
date: 2018-11-22
category: 技术工具
---

~~这篇post会持续更新（如果有的话）。~~

2020-6-6补充：已经坑啦，因为现在绝大多数代码都是在Linux下面写了，以后有空写个Linux的配置吧。

# CodeRunner

用来直接运行代码的插件，需要自己安装好MinGW的编译器环境。  
运行的环境不支持UTF-8，所以要改配置在终端中运行，配上chcp 65001。  

# GDB调试

直接配置调试是不行的，必须新建一个Task。  
tasks.json配置如下：
```
{
    "version": "2.0.0",
         "tasks": [
             {
                "label": "test",
                 "type": "shell",
                 "command": "g++",
                "args": ["-g", "${file}", "-o", "${workspaceRoot}/lab3.exe"]
            }
        ]
    }
```
然后launch.json配置如下：
```
{
    "version": "0.2.0",
    "configurations": [

        {
            "name": "(gdb) Launch",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/xxx.exe",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": true,
            "preLaunchTask": "test",
            "MIMode": "gdb",
            "miDebuggerPath": "D:\\Program Files\\mingw-w64\\x86_64-8.1.0-posix-seh-rt_v6-rev0\\mingw64\\bin\\gdb.exe",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ]
        }
    ]
}
```
