**目录**
[toc]

> 作者：来知晓
> 公众号：来知晓
> 简介：哈工大硕士，华为算法工程师，专注分享读书、职场、技术及自我提升等内容

标题：C学习：C语言快排函数qsort使用小结

> 包含在头文件 `<stdlib.h>`
> 重要提示：qsort排序结果是**不稳定的**，可能会导致相同大小的元素发生位置交换，需注意应用场景！

## 函数原型
----

```c
void qsort(void *base, size_t nmemb, size_t size, int(*compar)(const void *, const void *))
```
**参数说明：**
- base: 要排序的数组，如 `int*` 类型的指针或者数组首地址
- nmemb:数组元素个数，如 `int a[12]` 表示12个int类型元素
- size:每个元素所占内存空间，单位为字节byte，比如 `int` 类型通常为`2 byte`，常用`sizeof(int)/sizeof(int*)`获取
- compar:比较数组元素的比较函数
- 可以自动实现`int/float/double`等基本数据类型的排序
- 包含在`stdlib.h`头文件里

**附带函数：compar**
该函数通常是自定义的，名字自取，格式如下：
```c
int compar(const void *a,const void *b)
```
- 返回类型根据情况自定义，**返回值大于0时，a和b交换顺序**
- a和b是逻辑上比较，**不局限于数字，字符也可**
- a和b通常是指向不同元素的地址指针，运算时需要用 `*a` 解引用 

```c
- 若 a > b，返回 >0
- 若 a == b，返回 0
- 若 a < b，返回 <0
```


## 小例讲解
----
**例一：数组比值**

```c
#define NUMSIZE 5
int a[NUMSIZE] = {5, 2, 1, 3, 4};
int cmp(const void* a, const void *b) {
    // *(int*)a表示将a强转成int*类型后再解引用，得到其指向的值
	return *(int*)a - *(int*)b; // 升序 
	// return *(int*)b - *(int*)a; // 降序
}
qsort(a, NUMSIZE, sizeof(int), cmp); //执行后，数组a被升序排列
```

**例二：二维数组比值排序**

```c
int a[1000][50];  // 其中按照a[i][2]，第2列的数据大小作为排序依据，其中a[i][0]必须和a[i][49]一起，与其他行移动交换。
int comp(const void *a,const void *b)
{
     return ((int *)a)[2]-((int *)b)[2]; // 交换依据，比较某两个元素
}
// 以a为起始地址指针，根据compare结果决定起始位置往后sizeof(int)*2这一整块拷贝交换，也即整行交换
qsort(a,1000,sizeof(int)*50,comp);
```

**例三：结构体比值**

```c
// @example.c
#include <stdlib.h>  //for qsort function
struct ObjNums {
    int val;
    int index;
};
static int cmp(const void* a, const void* b)  //比较入参
{
    return ( *(struct ObjNums *)a ).val - ( *(struct ObjNums *) b).val; //从小到大排序，交换return的a，b则从大到小排序
}
void main() {
	struct ObjNums *sortedNums = (struct ObjNums *) malloc(numsSize * sizeof(*sortedNums));
	... //给sortedNums初始化赋值
	qsort(sortedNums, numsSize, sizeof(*sortedNums), cmp); // 排序,由小到大
}
```			

## 应用实例
----
以Leetcode的`twoSum`题的应用为例：

```c
struct ObjNums {
    int val;
    int index;
};

static int cmp(const void* a, const void* b)  //比较入参
{
    return ( *(struct ObjNums *)a ).val - ( *(struct ObjNums *) b).val; //从小到大排序，交换return的a，b则从大到小排序
}

int* twoSum(int* nums, int numsSize, int target, int* returnSize)
{
    int *ret = (int *) malloc( 2 * sizeof(int));
    struct ObjNums *sortedNums = (struct ObjNums *) malloc(numsSize * sizeof(*sortedNums));
    int i,j;
    *returnSize = 0; 

    for(i=0; i < numsSize; i++) { // 拷贝副本
        sortedNums[i].val = nums[i];
        sortedNums[i].index = i;
    }
    qsort(sortedNums, numsSize, sizeof(*sortedNums), cmp); // 排序,由小到大

    i = 0;
    j = numsSize - 1;
    while (i < j) { // 夹逼求和
        if (sortedNums[i].val + sortedNums[j].val < target) {
            i++; // 右移增大两者和
        }
        else if (sortedNums[i].val + sortedNums[j].val > target) {
            j--; // 左移减小两者和
        }
        else {
            ret[0] = sortedNums[i].index;
            ret[1] = sortedNums[j].index;
            *returnSize = 2; // 务必要有返回值大小，不然ret数组不知道输出多少个
            free(sortedNums);
            return ret;
        }
    }
    free(sortedNums);
    return ret; 
}
```

**注意事项**
 - qsort函数第一个参数为数组首地址
 - 对浮点型数据比较时，返回做差结果即可，大于0不动，小于0交换
 - 注意cmp函数的内部实现，保证为对应元素做差
 

## 参考资料
---
1. [重要！ C语言的快排函数qsort()](https://blog.csdn.net/qq_41799219/article/details/80626467)
2. [C语言快排函数qsort()](https://blog.csdn.net/f_zyj/article/details/51484751)
3. [C语言标准库函数qsort排序的介绍与使用](https://blog.csdn.net/masibuaa/article/details/5633498)



