**Ŀ¼**
[toc]

���⣺VSCODE�C/C++���̺�������

## ����˵��
---
 **�����ص㣺**
 - JSON(JavaScript Object Notation) �ǻ���JavaScript��һ�����ݽ�������
 - �����ļ���`tasks.json / launch.json`
 - �����ļ���`c_cpp_propertis.json / settings.json / compile_commands.json`
 - ���������C/C++

**��Ҫ���ã�**
�ļ��� | ��Ҫ�� | ����
--|--|--
tasks.json | ���� | �൱��gcc���벽����ܣ�Ҳ���Ե���makefile���������ɿ�ִ���ļ�
launch.json | ���� | �������gdb�����������п�ִ���ļ�
compile_commands.json | �Ǳ��� | ���ɱ������ݿ⣬���ڿ��ٱ���
c_cpp_properties.json | �Ǳ��� |���������������ã������������·��������·����C ++��׼��
settings.json | �Ǳ��� | ����IDE�༭������ҳ���񡢴����ʽ��������ɫ��С����Ĭ�����ɼ���

**���òο������ļ��ؼ��㣺**
 - �õ�demo�����ļ����ο�tasks.json��launch.json���޸Ķ�Ӧgdb/gcc·����Դ�ļ���ͷ�ļ���
 - ��֤lauch���õ�exe����tasks���ɵ�exe��һ�£��޸���Ϻ�`ctrl + shift + b`�����ն�->�����������񣬼�`����tasks.json������exe`
 - ��ݼ�F5���л��ߵ��������ڹ�Ӳ��Ű�ť�ҵ����Ͻ���ɫ���Ű�ť����ʼ���ԣ���`����lauch.json������exe`

## ����˵��
----
�Ա������е���`main.c`Ϊ�����������£�

**main.c**

```c
#include <stdio.h>
int main()
{
    printf("Hi World!\n");
    return 0;
}
```


**Ŀ¼�ṹ**

- .vscode
	- tasks.json
	- launch.json
- main.c


## �����ļ�
---
### tasks.json
ģ�����£�

```javascript
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Build", // ����lauch.json��"preLaunchTask"������һ��
            "type": "shell",
            "command": "D:\\xx\\mingw\\bin\\gcc.exe",
            "args": [
              "main.c",
              "-g",
              "-o",
              "main.exe" // ���exe����Ҫ��launch�е��õ�program������һ��
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
ģ�����£�

```javascript
{
    // ʹ�� IntelliSense �˽�������ԡ� 
    // ��ͣ�Բ鿴�������Ե�������
    // ���˽������Ϣ�������: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "gdbdebug",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}\\main.exe", // ���õ�exe����Ҫ��tasks���ɵ�exe��һ��
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": true, // ��������ǵ����ⲿ�ڴ�����ʾ��������IDE���ն˺ڴ�����ʾ
            "MIMode": "gdb",
            "miDebuggerPath": "D:\\xx\\mingw\\bin\\gdb.exe",
            "preLaunchTask": "Build", // ����������Ҫ��tasks.json�е�label������һ�£�������ÿ�ε���launchʱ���ȵ���tasks.json���Ӷ������Լ�ÿ�ζ�ctrl+shift+b�ֶ�������������󣬲��ܵ���launch
            "setupCommands": [
                {
                    "description": "Ϊ gdb ���������ӡ",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ]
        }
    ]
}
```
**��Ҫ����˵����**
- `program`ѡ��ΪҪ���Գ����·�����˴�Ϊmain.exe
- `args`Ϊ����ʱ��ӵĲ���
- `stopAtEntry`ѡ��Ĭ��Ϊfalse��������Ϊtrue������ں����������ͣ
- `externalConsole`Ϊ�Ƿ����ⲿ���������У�����Ϊtrue�ᵯ��windows�����д���
- `miDebuggerPath`Ϊgbd��������·��
- `setupCommands`Ϊ��������ǰΪGDB������������Ӧ������
- `preLaunchTask`ѡ��Ϊ���е���ǰִ�е�����

## ����json
---

```c c_cpp_propertis.json / settings.json / compile_commands.json```��ͨ������Զ����ɣ�������Ӧ�����õ��ڣ�����׸����

## ��������
----
**������ʾ�Ҳ���gcc����**
- ����tasks.json���label���ֺ�launch.json�е�preLaunchTask����û��Ӧ�ϣ����±����launch������Ӧexe
- ������tasks.json�Ǳ��룬launch.json������exe��tasks.json��lanuch֮ǰ��tasks���labelһ��Ҫ��launch.json�е�preLaunchTask���ֶ�Ӧһ�¡�֮���򲻻ᱨ�Ҳ���gcc�������

**���main������ڣ����±�����ֹ����ʾ��multiple definition of 'main'**
- ɾ�����й�.c�ļ����ɣ�����Щ��main�Ĳ�������롣
- ������tasks.json���ų�������main������ص�c�ļ������䲻������뼴�ɡ�



## �ο�����

 1. [json���ݸ�ʽ˵��](https://www.cnblogs.com/f-ck-need-u/p/10078072.html)
 2. [�����ļ����� -- JSON�ļ�����](https://blog.csdn.net/tbluhongxuan/article/details/110231712)
 3. [Visual Studio Code ��VSCode�� ֮ C/C++ �����������](https://blog.csdn.net/ZCShouCSDN/article/details/60466707)
 4. [VSCode����C/C++�̳�](https://blog.csdn.net/Zhangguohao666/article/details/104963520)
