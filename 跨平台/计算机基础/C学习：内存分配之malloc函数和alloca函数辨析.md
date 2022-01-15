
**目录**
[toc]

标题：C学习：内存分配之malloc函数和alloca函数辨析

简单总结下这两个函数的本质区别，具体的使用方法可去参考资料做进一步研究。


## 相同点
---
 - `都是动态分配`，根据实际运行情况动态分配的内存，而非一开始就分配
 - `返回类型相同`，都是返回的内存起始地址
 - `内存连续`，一次调用后，分配的内存都是逻辑连续的
 - `调用相同`，输入参数都是申请内存的byte数

## 不同点
---
- `内存分区不同`，malloc分配的是堆区内存，需要手动free；alloca分配的是栈区内存，程序自动释放；（注意，栈空间有限仅几kb左右，堆空间远大于栈空间）
- `作用域不同`，malloc分配的内存全局可用（类似全局变量），alloca分配的内存仅当前函数可用（类似局部变量）
- `可移植性不同`，malloc是C标准库函数<stdlib.h>里的，可移植通用；alloca在"malloc.h"里，不具有可移植性，一般禁用

## 补充
---

 - `alloca()`: Allocation，分配一个连续栈内存
 - `malloc()`:  Memory Allocation，分配一个连续堆内存
 - `calloc()`:Clear Allocation，与malloc的最大区别就是分配后的内存默认初始化清零
 - `realloc()`:Reset Allocation，给已分配的堆内存重新调整大小

## 参考资料
---
1. [基于栈的内存分配 —— alloca](https://cloud.tencent.com/developer/article/1729074)
2. [菜鸟教程：C++ 动态内存](https://www.runoob.com/cplusplus/cpp-dynamic-memory.html)
3. [怎样深入理解堆和栈](https://zhuanlan.zhihu.com/p/66922957)
4. [扩展：malloc和calloc区别](https://blog.csdn.net/qq_35608277/article/details/79467539)
5. [C语言calloc()函数：分配内存空间并初始化](http://c.biancheng.net/cpp/html/134.html)