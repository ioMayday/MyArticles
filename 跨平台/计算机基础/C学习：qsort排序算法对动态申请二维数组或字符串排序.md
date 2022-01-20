
**目录**
[toc]

标题：C学习：qsort排序算法对动态申请二维数组或字符串排序

> 附：[C代码在线调试工具](https://c.runoob.com/compile/11)

## 前言
-----

由于**动态申请的二维数组相邻行的内存空间并不连续**，所以会导致`qsort()`操作连续内存时有些变化，如不注意会导致报`segment fault`错误。对动态申请的不同字符串长度进行排序时，也有类似情况。下面结合实例代码进行分析。

以下代码需包含头文件：

```c
#include <stdio.h>
#include <stdlib.h>
```


## 二维数组排序
---
**样例1**

默认升序排序，按第一列大小作为依据对每行排序。要点如下：

 - 从代码中可以看出，**需要注意**修改`qsort()`的第3个参数元素的大小，要改成一级指针所占空间大小，以及对应的`compar`函数，因为输入的第一个参数是二级指针。
 - 此时，`qsort()`排序的**本质**是对连续分配内存的row个二级指针进行排序，从而交换每行一级指针的位置，并未对每行一级指针对应的内存进行操作。
 - 对**多个不同长度的字符串排序也是类似道理**，只对字符串一级指针做交换排序。

```c
// 错误写法
int CompareIntArray(const void *a, const void *b)
{
    return (*(int *)a) - (*(int *)b);
}

// 正确写法，对二级指针解引用两次得到第一列值
int CompareIntArray2(const void *a, const void *b)
{
    return *(*(int **)a) - *(*(int **)b);
}

// Test 2D Array
int main(void)
{
    int a[5][3] = {{5,4,-1},{3,2,3},{7,3,-2},{-1,1,1},{4,0,1}};

    int row = 5;
    int col = 3;

    // 申请指向行首指针的二级指针空间
    int **arr2 = (int **)malloc(row * sizeof(int*));
    int i, j;
    for (i = 0; i < row; i++) { 
        // 得到行首指针，注意相邻行的内存空间不一定连续
        // 但同一行内部元素的内存空间是连续的
        arr2[i] = (int*)malloc(col * sizeof(int));
    }

    // 录入数据
    for (i = 0; i < row; i++) {
            for (j = 0; j < col; j++) {
            arr2[i][j] = a[i][j];  // 快速录入预设数据
            // int ret = scanf("%d", &arr2[i][j]);
            // if (ret != 1) { return -1; }
        }
    }
    arr2[2] = (int *)&b[0];  // 替换第3行数据

    // 打印输入的数组
    for (i = 0; i < row; i++) {
        for (j = 0; j < col; j++) {
            printf("%d ", arr2[i][j]);
        }
        printf("\n");
    }
	printf("\n\n");
	
	// 常见错误调用，操作后打印时会报错
    // qsort(arr2, 5, 3 * sizeof(int), CompareIntArray); 
    // 正确调用，注意元素大小和compare函数
    qsort(arr2, row, sizeof(int*), CompareIntArray2); 

    // 打印排序后的数组
    for (i = 0; i < row; i++) {
        for (j = 0; j < col; j++) {
            printf("%d ", arr2[i][j]);
        }
        printf("\n");
    }

    // 释放对应内存
    for (i = 0; i < row; i++) {
    	// 当有 arr2[2] = (int *)&b[0]
    	// 注意不能释放arr2[2], 因为是从栈中申请的，不是在堆里动态申请来的内存
        free(arr2[i]);  
    }
    free(arr2);
   
    return 0;
}
```
输出效果：

```c
5 4 -1 
3 2 3 
10 3 1 
-1 1 1 
4 0 1 


-1 1 1 
3 2 3 
4 0 1 
5 4 -1 
10 3 1
```

**样例2**
- 输入：动态申请的二维数组a[3][2]。
- 功能：当每行的第一个数来比较，若不等时按升序，若相等时，按每行第二个数来比较，按降序排列。

代码如下：

```c
/*
1、先排序，起始从小到大，相同时终点从大到小
2、分情况进行合并，双指针left/right来移动并判断新区间是否在里面，并合并
*/

int CompareIntArray2(const void *a, const void *b)
{
    int ret = *(*(int **)a) - *(*(int **)b); // 升序
    if (ret == 0) { // 起点相同时按终点来比较
        ret = *(*(int **)b + 1) - *(*(int **)a + 1); // 降序
    }
    return ret;
}

int removeCoveredIntervals(int** intervals, int intervalsSize, int* intervalsColSize)
{
    printf("%d\n", intervalsSize);
    printf("%d\n", *intervalsColSize);
    int num = 0;
    qsort(intervals, intervalsSize, sizeof(int*), CompareIntArray2);
    int i, j;
    int row = intervalsSize;
    int col = *intervalsColSize;
    for (i = 0; i < row; i++) {
        for (j = 0; j < col; j++) {
            printf("%d ", intervals[i][j]);
        }
        printf("\n");
    }

    return num;
}
```




## 字符串排序
----
默认采用`字典序排序`。

```c
int CompareStr(const void *a, const void *b)
{
    return strcmp((const char *)a, (const char *)b);
}

// 实际进来的是字符串二级指针
int CompareStr2(const void *a, const void *b)
{
    return strcmp(*(char **)a, *(char **)b);  // 取指向的一级指针对应字符串做比较
}

// Test 2D String
int main(void)
{
    const char *name[] = {"Jerry", "Tom", "Toby", "Ada"};       // 指针数组，name本质是二级指针
    const char name2[4][256] = {"Berry", "Tom", "Toby", "Ada"}; // 二维字符串数组，留冗余空间给每个字符串

    int row = 4;
    int col = 10;

    // 申请指向行首指针的二级指针空间
    char **str2 = (char **)malloc(row * sizeof(int**));
    int i, j;
    for (i = 0; i < row; i++) { 
        // 得到行首指针，注意相邻行的内存空间不一定连续
        // 但同一行内部元素的内存空间是连续的
        str2[i] = (char*)malloc(col * sizeof(char));
    }

    // 录入数据
    for (i = 0; i < row; i++) {
      		// 常规录入数据，拷贝数据至动态分配内存，最后需手动free
      		memcpy(str2[i], &name2[i][0], col);
      		
      		// 快速录入预设数据，直接取得已静态分配内存的地址，注意后面不要free
            // str2[i] = (char *)name[i];
            // str2[i] = (char *)name2[i];
    }

    // 打印输入的字符串
    for (i = 0; i < row; i++) {
        printf("%s\n", str2[i]);
    }
    printf("\n");

    // 错误调用，运行后printf时会报segment fault，说明错误操作了内存
    // qsort(str2, row, col * sizeof(int), CompareStr);
    // 正确调用，对字符串一级指针排序
    // row = sizeof(name) / sizeof(name[0]); // 得到指针个数
    qsort(str2, row, sizeof(char*), CompareStr2);  // 注意元素的空间大小要变成指针所占大小

    // 打印按字典序排序后的字符串
    for (i = 0; i < row; i++) {
        printf("%s\n", str2[i]);
    }
    printf("\n");

    // 释放动态分配的内存
    for (i = 0; i < row; i++) { 
        free(str2[i]);
    }
    free(str2);
   
    return 0;
}
```
输出效果：

```c
Berry
Tom
Toby
Ada

Ada
Berry
Toby
Tom
```

重要说明：
```c
char *name[] = { "Toby", "Tony", ""Michael", "Canndy"};
```

 - `*name[]`定义了一个指针数组，数组里存着字符串常量对应的地址，`name`本质是个二级指针。
 - 可以通过 `sizeof(name) / sizeof(name[0])`，获得指针个数，`sizeof(name)`表示指针数组所占总空间， `sizeof(name[0])`表示一个指针所占空间。

## 要点总结
-----

**二级指针申请到的字符串或者数组如何有效排序？**
 - 二级指针字符串，务必要保证连续的二级指针共n个，排序本质是对不同长度的字符串首地址的各指针进行排序
 - 二维数组本质也是如此，关键在于`compar`比较函数一些细节的写法

 



## 相关资料
------

1. [C学习：qsort快排函数在二维数组中的灵活应用](https://blog.csdn.net/qq_17256689/article/details/108937155)
2. [C学习：快排函数使用小结 qsort](https://blog.csdn.net/qq_17256689/article/details/108567201)
3. [通俗易懂，直指本质：用qsort对字符串数组排序需要注意的几个问题](http://blog.chinaunix.net/uid-15723764-id-3366757.html)