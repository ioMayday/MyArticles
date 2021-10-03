**目录**
[toc]

标题：VSCODE搭建C/C++工程核心配置

## 总体说明
---
 **总体特点：**
 - JSON(JavaScript Object Notation) 是基于JavaScript的一种数据交换语言
 - 核心文件：`tasks.json / launch.json`
 - 其他文件：`c_cpp_propertis.json / settings.json / compile_commands.json`
 - 辅助插件：C/C++

**主要作用：**
文件名 | 重要性 | 作用
--|--|--
tasks.json | 必需 | 相当于gcc编译步骤汇总，也可以调用makefile，最终生成可执行文件
launch.json | 必需 | 用于添加gdb调试任务，运行可执行文件
compile_commands.json | 非必需 | 生成编译数据库，便于快速编译
c_cpp_properties.json | 非必需 |将允许您更改设置，例如编译器的路径，包含路径，C ++标准等
settings.json | 非必需 | 设置IDE编辑器，如页面风格、代码格式、字体颜色大小，按默认生成即可

**复用参考配置文件关键点：**
 - 拿到demo配置文件，参考tasks.json和launch.json，修改对应gdb/gcc路径和源文件、头文件等
 - 保证lauch调用的exe名与tasks生成的exe名一致，修改完毕后，`ctrl + shift + b`或点击终端->运行生成任务，即`调用tasks.json，生成exe`
 - 快捷键F5运行或者点击左侧栏乌龟加播放按钮找到左上角绿色播放按钮，开始调试，即`调用lauch.json，运行exe`

## 举例说明
----
以编译运行单个`main.c`为例，配置如下：

**main.c**

```c
#include <stdio.h>
int main()
{
    printf("Hi World!\n");
    return 0;
}
```


**目录结构**

- .vscode
	- tasks.json
	- launch.json
- main.c


## 核心文件
---
### tasks.json
模板如下：

```javascript
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Build", // 需与lauch.json中"preLaunchTask"项命名一致
            "type": "shell",
            "command": "D:\\xx\\mingw\\bin\\gcc.exe",
            "args": [
              "main.c",
              "-g",
              "-o",
              "main.exe" // 输出exe名，要与launch中调用的program项命名一致
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            // Use the standard MS compiler pattern to detect errors, warnings and infos
            "problemMatcher": [
                "$gcc"
            ]
        }
    ]
}
```

### launch.json
模板如下：

```javascript
{
    // 使用 IntelliSense 了解相关属性。 
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "gdbdebug",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}\\main.exe", // 调用的exe名，要与tasks生成的exe名一致
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": true, // 决定输出是单独外部黑窗口显示，还是在IDE里终端黑窗口显示
            "MIMode": "gdb",
            "miDebuggerPath": "D:\\xx\\mingw\\bin\\gdb.exe",
            "preLaunchTask": "Build", // 此项命名需要与tasks.json中的label项命名一致，作用是每次调用launch时会先调用tasks.json，从而不用自己每次都ctrl+shift+b手动生成最新任务后，才能调用launch
            "setupCommands": [
                {
                    "description": "为 gdb 启用整齐打印",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ]
        }
    ]
}
```
**重要参数说明：**
- `program`选项为要调试程序的路径，此处为main.exe
- `args`为运行时添加的参数
- `stopAtEntry`选项默认为false，若设置为true，则会在函数入口中暂停
- `externalConsole`为是否在外部控制外运行，设置为true会弹出windows的运行窗口
- `miDebuggerPath`为gbd调试器的路径
- `setupCommands`为启动调试前为GDB调试器设置相应的命令
- `preLaunchTask`选项为运行调试前执行的任务

## 其他json
---

```c c_cpp_propertis.json / settings.json / compile_commands.json```可通过插件自动生成，并做相应的配置调节，不再赘述。

## 常见报错
----
**编译提示找不到gcc任务**
- 根因：tasks.json里的label名字和launch.json中的preLaunchTask名字没对应上，导致编译后launch不到对应exe
- 分析：tasks.json是编译，launch.json是运行exe，tasks.json在lanuch之前。tasks里的label一定要跟launch.json中的preLaunchTask名字对应一致。之后则不会报找不到gcc任务错误。

**多个main函数入口，导致编译中止。提示：multiple definition of 'main'**
- 删除掉有关.c文件即可，让这些含main的不参与编译。
- 或者在tasks.json中排除掉其余main函数相关的c文件，让其不参与编译即可。



## 参考资料

 1. [json数据格式说明](https://www.cnblogs.com/f-ck-need-u/p/10078072.html)
 2. [常用文件类型 -- JSON文件介绍](https://blog.csdn.net/tbluhongxuan/article/details/110231712)
 3. [Visual Studio Code （VSCode） 之 C/C++ 调试配置详解](https://blog.csdn.net/ZCShouCSDN/article/details/60466707)
 4. [VSCode配置C/C++教程](https://blog.csdn.net/Zhangguohao666/article/details/104963520)
