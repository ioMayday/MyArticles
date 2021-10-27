**目录**
[toc]

标题：C刷题：LeetCode 633. 二分查找应用之平方数之和，二分查找法应用

> 作者：来知晓
> 公众号：来知晓
> 刷题交流QQ群：444172041

## 题目描述

------


> 核心说明：给定一个非负整数 `c` ，你要判断是否存在两个整数 `a` 和 `b`，使得 `a^2 + b^2 = c` 。
>
> 题目链接：[633. 平方数之和](https://leetcode-cn.com/problems/sum-of-square-numbers/)



## 思路分析

--------



认真阅读，理解题意，整理如下：

- 核心关系式：`a^2 + b^2 = c`
- 为简化问题，a, b可直接认为非负，范围为:` [0, sqrt(c)]`
- 特殊用例，按提示就是c的两个边界值`0`和`2^15-1`

可得到两条思路，如下：

**思路1**

- 直接配凑法
- 两层循环+二分优化
- 内外层分别从小到大，`0 -> sqrt(c)`
- 时间复杂度：`O(NlogN)`

**思路2**

- 借用sqrt函数
- 外层a从0 到 `sqrt(c)`
- 判断`(c - a^2)`开方后
- 取整的结果前后两个值配凑是否等于c
- 时间复杂度：`O(N)`

## 代码实现

--------



根据思路一，先实现无优化的`O(N^2)`代码版本如下：

```c
// 原始版本，时间复杂度O(N^2)，提交显示超时限制
bool judgeSquareSum(int c){
    float cf = sqrt(c);
    // printf("%f\n", cf);
    long long cfInt = (long long)cf;
    // printf("%ld\n", cfInt);
    long long target = (long long)c;
    long long a, b;
    for (a = 0; a <= cfInt; a++) {
        for (b = 0; b <= cfInt; b++) {
            if (a * a + b * b == target) {
                // printf("a=%lld, b=%lld\n", a, b);
                return 1;
            }
        }
    }
    return 0;
}

```



思路一中利用二分法实现优化后的`O(NlogN)`代码版本如下：

```c
// 二分优化, 时间复杂度O(NlogN)
bool judgeSquareSum(int c){
    float cf = sqrt(c);
    long long cfInt = (long long)cf;
    long long target = (long long)c;
    long long a;
    for (a = 0; a <= cfInt; a++) {
        long long left, right;
        left = 0;
        right = cfInt + 1;
        long long a2 = a * a;
        while (left < right) {
            long long mid = left + (right - left) / 2;
            if (a2 + mid * mid == target) {
                return 1;
            } else if (a2 + mid * mid < target) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
    }
    return 0;
}

```



思路二，遍历+前后验证，`O(N)`代码版本如下：

```c
// 算法优化, 时间复杂度O(N)，不计入sqrt开销
#define DOUBLE_MIN (1e-15)
bool judgeSquareSum(int c){
    float cf = sqrt(c);
    long long cfInt = (long long)cf;
    long long target = (long long)c;
    // long long a;
    // for (a = 0; a <= cfInt; a++) {
    //     long long a2 = a * a;
    //     float bTmp = sqrt(c - a2);
    //     long long bTmpInt = (long long)bTmp;
    //     if (bTmpInt * bTmpInt + a2 == target || // 会出现整型溢出问题，输入99999999
    //         (bTmpInt + 1) * (bTmpInt + 1) + a2 == target ||
    //         (bTmpInt - 1) * (bTmpInt - 1) + a2 == target) {
    //         printf("a=%lld, b=%lld\n", a, (long long)(sqrt(c - a2)));
    //         return 1;
    //     }
    // }

    long long a;
    for (a = 0; a <= cfInt; a++) {
        long long a2 = a * a;
        float bTmp = sqrt(c - a2);
        long long bTmpInt = (long long)bTmp;
        // printf("%f\n", (double)bTmpInt * bTmpInt);
        double it1 = (double)a2 - (double)target;
        double sub1 = fabs((double)bTmpInt * bTmpInt + it1);
        double sub2 = fabs(((double)bTmpInt + 1) * ((double)bTmpInt + 1) + it1);
        double sub3 = fabs(((double)bTmpInt - 1) * ((double)bTmpInt - 1) + it1);
    
        if (sub1 < DOUBLE_MIN ||
            sub2 < DOUBLE_MIN ||
            sub3 < DOUBLE_MIN) {
            printf("a=%lld, b=%lld\n", a, (long long)(sqrt(c - a2)));
            return 1;
        }
    }
    
    return 0;
}
```



**大家再想想，代码还能再优化吗，还能更快更强些吗？**

自己研究半天后，看了下官方解答，发现还有假定`a <= b`结合双指针的思路来实现`O(N)`，还有一种利用费马平方和定理的数学方法来判断是否存在，但都无法超过`O(N)`的时间复杂度。

## 参考资料

------



1. [LeetCode官方题解：平方数之和](https://leetcode-cn.com/problems/sum-of-square-numbers/solution/ping-fang-shu-zhi-he-by-leetcode-solutio-8ydl/)

