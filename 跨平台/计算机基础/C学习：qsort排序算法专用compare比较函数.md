
**目录**
[toc]

标题：C学习：qsort排序算法专用compare比较函数


> 附：[C代码在线调试工具](https://c.runoob.com/compile/11)

## 核心说明
----

说明：
 - `qsort()`被包含在头文件 `<stdlib.h>`
 - 以下比较函数默认是升序排列，若想降序排列，交换函数实现里的a和b位置即可。
 - qsort算法不具有稳定性，排序时，相同大小元素相对位置可能会发生改变。
 - qsort只能针对不要求排序稳定性的场合使用，也即仅对元素排序，元素对应的位置没有意义。

## 数组的compare
------

一维数组：
```c
// 对一维数组按各元素大小，升序排列
int CompareIntArray(const void *a, const void *b)
{
    return (*(int *)a) - (*(int *)b);
}
```
二维数组：

```c
// 对二维数组以其中某列元素大小为依据，对各行进行排序
int CompareIntMatrix(const void *a, const void *b)
{
    // 其中2可以改为任意一列，以其大小作为每行排列的依据
    return ((int *)a)[2] - ((int *)b)[2];
}
```

## 字符串的compare
------

按字典序排序：

```c
// 按字典序对多个字符串排序，单个字符串本质是1维的
int CompareStr(const void *a, const void *b)
{
    return strcmp((const char *)a, (const char *)b);
}
```

## 结构体的compare
------

对结构体数组的排序：
```c
struct ObjNums {
    int val;
    int index;
};

static int CompareStru(const void* a, const void* b)
{
    return ( *(struct ObjNums *)a ).val - ( *(struct ObjNums *) b).val;
}
```

## 相关资料
-----
1. [C学习：qsort快排函数在二维数组中的灵活应用](https://blog.csdn.net/qq_17256689/article/details/108937155)
2. [C学习：快排函数使用小结 qsort](https://blog.csdn.net/qq_17256689/article/details/108567201)








