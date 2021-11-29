**目录**
[TOC]

标题：C学习：从extern深入了解声明和定义的区别
> 起因来源于，在头文件中extern声明全局变量，编译报错。以下根据问题现象和分析来说明extern的用法，以及深入理解声明和定义的区别。


## 1 背景
----
在a.h中定义了结构体类型如下，

```c
typedef struct igf_enc_instance_struct
{
    IGF_ENC_PRIVATE_DATA  igfData;
    int                   infoSamplingRate;
    int                   infoStartFrequency;
    int                   infoStopFrequency;
    int                   infoStartLine;
    int                   infoStopLine;
    int                   infoFrameCount;
    int                   flatteningTrigger;
} IGF_ENC_INSTANCE, *IGF_ENC_INSTANCE_HANDLE;
```

并同时声明了一个全局变量：

```c
extern IGF_ENC_INSTANCE g_igfInstance;
```

然后在b.c中包含a.h头文件，并引用如下：
```c
/* Initialize IGF */
memset(&g_igfInstance, 0, sizeof(g_igfInstance));
g_igfInstance.infoStopFrequency = -1;
// 标志位如果打开IGF则  
IGFEncSetMode(&g_igfInstance, st->paramInter.bitRate, 2, 0);
```

## 2 报错现象
----

显示b.c中`undefined reference to 'g_igfInstance' `

## 3 原因分析
----

`g_igfInstance`在`a.h`里只是被声明了，其实并没有定义，也没有分配内存（我误以为定义了），所以最好应该在`a.c`中定义（该全局变量视情况决定是否赋初值，否则全局变量默认指针为`NULL`，值为`0`，再在`a.h`中声明 extern。

**小结：**

 - 定义变量和声明变量的**区别**在于**定义会产生内存分配的操作**
-  **定义是汇编阶段**的概念
-  **声明是链接阶段**的概念，告诉包含该声明的模块，链接时要从其它模块寻找外部函数和变量
- **定义最好在.c文件中实现，声明在.h里实现**，否则多个c文件重复包含该头文件时，会报错重复定义
- c文件中include 头文件，**本质只是把头文件内容像宏一样展开**，只有字符串替换过程

`定义和声明的区别辨析`，更多例子帮助理解：

```c
extern int a;		//声明一个全局变量a
int a;      		//定义一个全局变量a
extern int a = 0;	//定义一个全局变量a 并给初值
int a = 0;			//定义一个全局变量a 并给初值
```

## 3 解决
----
a.c中定义，`IGF_ENC_INSTANCE g_igfInstance;`
a.h中声明，`extern IGF_ENC_INSTANCE g_igfInstance;`
b.c中正常包含a.h，`include "a.h"`


## 4 参考资料
----

1. [extern 与头文件(*.h)的区别和联系](https://www.runoob.com/w3cnote/extern-head-h-different.html)


