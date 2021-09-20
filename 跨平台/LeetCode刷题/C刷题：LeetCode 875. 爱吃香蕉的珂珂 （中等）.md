**目录**
[toc]


标题：C刷题：LeetCode 875. 爱吃香蕉的珂珂 （中等）

## 解题思路
---
**分析：**
	
本题的每堆香蕉数可排序，也可以不排序。虽然本题自己写一个查找最大值的函数仅需一个for循环，时间复杂度`O(N)`，比快排的时间复杂度为`O(NlogN)`消耗要小。但还是**建议先排序**，排序的好处是直接能找出最值，不用自己写函数，另一个也可以简化思路。**下面分析中默认已按升序排序。**

题目中，目标是求珂珂在警察回来之前吃完香蕉的最小速度，而题目中香蕉的堆数`N`小于等于警察回来的时间`H`的，也即珂珂只要按堆数里最大香蕉数速度来吃，肯定能在警察回来前或回来时吃完。这里我们找到了最大速度，也即上界。

所以，本题是一定有解的，且已经划定解的范围，为1到最大香蕉数，也即对应排序后的`[1, piles[numsSize - 1]]`。

注意，这里面有个小坑是，不能按照排序后的`piles[0]`作为起始点，因为假如警察回来的时间`H`足够长，完全可以按照比`piles[0]`还小的吃香蕉速度吃，也能吃完。


**思路：**

 - 在`[1, piles[numsSize - 1]]`范围中，遍历速度`k` 
 - 判断速度`k`是否能在`H`小时内吃完


## 暴力法版本
---
ver1时间复杂度为`O(N^2)`， 先排序直接遍历，提交显示超时，但用例通过比例为`111/118`。
```c
int Compar(void *a, void *b)
{
    return *(int *)a - *(int *)b;
}

int IsOk(int* piles, int pilesSize, int h, int k)
{
    int i;
    int cnt = 0;
    for (i = 0; i < pilesSize; i++) {
        cnt += piles[i] / k;
        if (piles[i] % k != 0) { // if remains
            cnt++;
        }

        if (cnt > h) {
            return 0; // fail
        }
    }   
    return 1;
}

int minEatingSpeed(int* piles, int pilesSize, int h)
{
    if (piles == NULL || pilesSize <= 0) {
        return -1;
    }
    qsort(piles, pilesSize, sizeof(int), Compar);
    int maxVal = piles[pilesSize - 1];

    int i;
    for (i = 1; i < maxVal; i++) {
        if (IsOk(piles, pilesSize, h, i)) {
            return i;
        }
    }
    return maxVal; // 直接按最大数目的堆数吃肯定能搞完
}
```

## 二分法优化版本
----
本题由于数据规模在`10^9`左右，所以`O(N^2)`的方法会超时，需要用二分法优化。

由于`[1, piles[numsSize - 1]]`该范围有个特点，某个值右边速度足够快全都满足，某个值左边速度慢不能满足，有单调性，满足二分法的可行解与解空间的关系。故转化成二分法查找左边界解法。

**代码如下**，时间复杂度`O(NlogN)`：
```c
int Compar(void *a, void *b)
{
    return *(int *)a - *(int *)b;
}

int IsNotOk(int* piles, int pilesSize, int h, int k)
{
    int i;
    int cnt = 0;
    for (i = 0; i < pilesSize; i++) {
        cnt += piles[i] / k;
        if (piles[i] % k != 0) { // if remains
            cnt++;
        }

        if (cnt > h) {
            return 1; // fail, not ok
        }
    }   
    return 0; // ok
}

int minEatingSpeed(int* piles, int pilesSize, int h)
{
    if (piles == NULL || pilesSize <= 0) {
        return -1;
    }
    qsort(piles, pilesSize, sizeof(int), Compar);
    int maxVal = piles[pilesSize - 1];

    // printf("maxVal = %d\n", maxVal);

    int left = 1;
    int right = maxVal + 1;
    int mid;
    while (left < right) {// 找符合条件的左边界
        mid = left + (right - left) / 2;
        // printf("mid = %d\n", mid);
        if (IsNotOk(piles, pilesSize, h, mid)) { // <,不满足条件
            left = mid + 1;
        } else { // >= 满足条件
            right = mid;
        }
    }
    return left;
}
```

## 参考资料
---
1. 二分总结：[C刷题：二分查找原始版、查找左侧边界/右侧边界模板大总结 | 二分法](https://blog.csdn.net/qq_17256689/article/details/119007921)

