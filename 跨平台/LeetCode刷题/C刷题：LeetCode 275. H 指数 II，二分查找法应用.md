**文章目录**
[toc]

标题：C刷题：LeetCode 275. H 指数 II，二分查找法应用

> 作者：来知晓
> 公众号：来知晓
> 刷题交流QQ群：444172041

## 题目描述

------



> **核心说明**：
>
> 给你一个整数数组 citations ，其中 citations[i] 表示研究者的第 i 篇论文被引用的次数，citations 已经按照 升序排列 。计算并返回该研究者的 h 指数。
>
> h 指数的定义：h 代表“高引用次数”（high citations），一名科研人员的 h 指数是指他（她）的 （n 篇论文中）总共有 h 篇论文分别被引用了至少 h 次。且其余的 n - h 篇论文每篇被引用次数 不超过 h 次。
>
> **提示**：如果 h 有多种可能的值，h 指数 是其中最大的那个。要求时间复杂度不大于`O(logN)`
>
> **题目链接**：[275. H 指数 II，中等](https://leetcode-cn.com/problems/h-index-ii/)



## 思路分析

--------



认真阅读，理解题意，整理如下：

- 核心即`文章数<=引用数`
- 下标idx的引用数为cita[idx]，设包含idx及往后的文章数n
- 若有 `n <= cita[idx]`，则`n`可以认为是其中的一个h指数值


可得到两条思路，如下：

**思路1**

- 从后往前，找到第一个符合条件的，即为最大H值
- 时间复杂度`O(N)`

**思路2**

- 先实现最基础的查找到正确的h值
  - 满足`n <= cita[idx]`即退出二分查找
- 再优化，若正确的值有过个，找最大的H值
  - H值最大时，在数组中的下标最小
  - 故找到值后不退出，一直缩放右边界
  - 从而找到满足条件时的最大值
- 时间复杂度：`O(logN)`

## 代码实现

--------

思路1实现较为简单，但不符合题目要求，这里略过。重点研究思路2。

先实现找到H值的基础二分代码：

```c
int hIndex(int* citations, int citationsSize)
{
    int left = 0;
    int right = citationsSize;
    while (left < right) {
        int mid = left + (right - left) / 2;
        int tmpH = citationsSize - mid;

        // the condition that citations[mid - 1] <= tmpH satisfies simultaneously
        // becasue of the ascending order array
        if (citations[mid] >= tmpH) {
            return citationsSize - mid; // >=, include mid
        } else {
            left = mid + 1;
        }
    }
    return left;
}

```

再优化，找到H值的最大值基础二分代码：

```c
int hIndex(int* citations, int citationsSize)
{
    int left = 0;
    int right = citationsSize;
    while (left < right) {
        int mid = left + (right - left) / 2;
        int tmpH = citationsSize - mid;

        // the condition that citations[mid - 1] <= tmpH satisfies simultaneously
        // becasue of the ascending order array
        if (citations[mid] >= tmpH) {
            right = mid; // shrink right range
        } else {
            left = mid + 1;
        }
    }
    return citationsSize - right; // origin: citationsSize - mid
}
```

附自行测试的主调demo：

```c
int main(void)
{
    int ret;
    int cita1[5] = {0, 1, 3, 5, 6};
    ret = hIndex(cita1, 5);
    printf("ret = %d\n", ret); // 3

    int cita2[3] = {1, 2, 100};
    ret = hIndex(cita2, 3);
    printf("ret = %d\n", ret); // 2

    int cita3[2] = {0, 2};
    ret = hIndex(cita3, 2);
    printf("ret = %d\n", ret); // 1

    int cita4[1] = {0};
    ret = hIndex(cita4, 1);
    printf("ret = %d\n", ret); // 0

    return 0;
}
```

提交后发现执行用时仅超过30%的提交，用时20ms。寻思难道还有更快的方法，研究了下执行用时最少的代码实现如下，发现时间复杂度依然是`O(logN)`，跟个人实现差异就是除以2，用的是移位。

```c
int lowerBound(const int* nums, int len) {
    int low = 0, high = len-1;
    while (low < high) {
        int mid = (low+high) >> 1; // different
        if (len-mid > nums[mid]) {
            low = mid + 1;
        } else {
            high = mid;
        }
    }
    
    return low;
}

int hIndex(int* citations, int citationsSize){
    int n = citationsSize, idx = lowerBound(citations, n);
    return citations[idx] ? n-idx : 0;
}

```

所以，得出结论，LeetCode的执行用时看看就好，并不准确，重点把算法实现的时间复杂度降低即可。另外，未优化的基础二分代码中，存在的遗留问题有：

- 升序里，仅`citations[mid] >= tmpH`，不能同时就保证`citations[mid - 1] <= tmpH`
- 前者并不能保证大于后，左边这个数不大于tmpH
- 只能说只是找到一个h值，这个h值最大最小都不确定



## 参考资料

------

无。