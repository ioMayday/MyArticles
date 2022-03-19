
标题：Python实践：一键批量替换不同文本字符串

**目录**
[toc]

## 问题背景

-----



由于部分项目有平台兼容性要求，为了使代码可移植，C代码中可能会利用宏命名，来控制类型定义。

如何将当前代码数据类型，批量替换为重定义后通用类型，成了我们要解决的问题。于是编写了以下Python脚本。

## 功能实现

----



**功能需求：**

-   输入：
    -   给定两个相互映射的类型定义字符串集
    -   给定待转换的批量C代码文本文件，以`UTF8`编码
-   输出：
    -   对同名文件进行修改，输出类型替换后的文本

**实现思路：**

-   前处理：得到所有文件名字
-   根据模式设置，确定老换新，还是新换老
-   遍历所有文件，处理单个文件类型替换
    -   核心子函数：单文件处理
        -   取待替换类型字符串个数
        -   获得单个文件所有行文本
        -   对每行文本进行替换操作
        -   将替换后文本写入源文件

**Python代码**

```python
# -*- coding: utf-8 -*-
"""
Created on Mon Mar 14 2022
@author: 来知晓NowKnow
filename: repalce_type_define.py
"""

import sys
import os


# repalce str_a with str_b
def file_process(file_path, a, b):
    print(file_path)
    lens = len(a)
    f = open(file_path, 'r+', encoding='utf-8')
    all_the_lines = f.readlines()
    f.seek(0)         # 移动文件指针，到最开始处
#    f.truncate()      # 从当前位置起，往后所有数据进行截断删除
    for line in all_the_lines:
        for i in range(lens):
            line = line.replace(a[i], b[i])
        f.write(line) # 向文件中按行写入替换后的文本
    f.close()
    return


# mode 0/1
# 0表示老到新，1表示新到老，其他则报错提示
def typedef_switch(dirc, old_type, new_type, mode):
    if mode != 0 and mode != 1:
        print('Mode set error, mode={}'.format(mode))
        return
    
    a = []
    b = []
    if mode == 0:
        a = old_type
        b = new_type
    else:
        b = old_type
        a = new_type        

    filelist = os.listdir(dirc)
    for file in filelist:
        file_path = os.path.join(dirc, file)
        file_process(file_path, a, b)


if __name__ == '__main__':
    # an example
    old_type = ['TYPEDEF_UINT8', 'TYPEDEF_INT8', 'TYPEDEF_CHAR',
              'TYPEDEF_UINT16', 'TYPEDEF_INT16',
              'TYPEDEF_UINT32', 'TYPEDEF_INT32',
              'TYPEDEF_UINT64', 'TYPEDEF_INT64',
              'TYPEDEF_FLOAT', 'TYPEDEF_DOUBLE',
              'TYPEDEF_UINTPTR', 'TYPEDEF_INTPTR',
              'TYPEDEF_NULL', 'TYPEDEF_VOID']
    new_type = ['uint8_t', 'int8_t', 'char',
              'uint16_t', 'int16_t',
              'uint32_t', 'int32_t',
              'uint64_t', 'int64_t',
              'float', 'double',
              'uintptr_t', 'intptr_t',
              'NULL', 'void']

    path1 = r'D:\workspace\src'
    typedef_switch(path1, old_type, new_type, 0)
    print('done!')

    # 正式退出main函数进程，以免main函数空跑
    sys.exit()

```

**注意**，以上代码需要保证每个文件均采用UTF8编码，且支持扩展，新老类型可相互转换，但要保证类型映射为一对一。

## 参考资料

-----



1. [Python批量替换文件中指定字符串](https://blog.csdn.net/sinat_36811967/article/details/86570911)