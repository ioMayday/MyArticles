**目录**
[toc]

标题：C刷题：LeetCode 1300. 转变数组后最接近目标值的数组和，二分查找法应用【中等】

> 作者：来知晓
> 公众号：来知晓
> 刷题交流QQ群：444172041

## 题目描述

------



> **核心说明**：
>
> 给你一个整数数组 arr 和一个目标值 target ，请你返回一个整数 value ，使得将数组中所有大于 value 的值变成 value 后，数组的和最接近  target （最接近表示两者之差的绝对值最小）。
>
> 如果有多种使得和最接近 target 的方案，请你返回这些整数中的最小值。
>
> 请注意，答案不一定是 arr 中的数字。
>
> **数据范围：**
>
> - `1 <= arr.length <= 10^4`
> - `1 <= arr[i], target <= 10^5`
>
> **题目链接**：[1300. 转变数组后最接近目标值的数组和](https://leetcode-cn.com/problems/sum-of-mutated-array-closest-to-target/)

疑问：啥情况下，value会有多个值？

## 思路分析

--------



认真阅读，理解题意，整理如下：

- 目标是找到一个最小的value
- 当数组中所有大于value的值，都变成value
- 使数组和减小，然后新数组和最接近target
- 有此等式：`value<=target` 和 `value<=maxVal`，且`value >= 0 `
- 特殊用例，题目保证是一定有解的

我们知道，一旦要求某数组不同区间数值和时，一定要想到前缀和数组。一旦要用二分法时，最好先排序，从而使后部分大于value的值可以迅速得到结果。

于是，可得思路如下：

- 先qsort排序，再对数组求前缀和

- 分类讨论数组和sum和target的关系

  - 若 `sum < target`，无解, 找到一个之差
  
  - 若 `sum = target`，最小的value就是数组最大值

  - 若 `sum > target`，则有两种思路

    - 思路一：查看差值，不好定减多少， pass掉

    - 思路二：二分法配凑
      - value从0开始，对应的sum最小
      - value累加到maxVal，对应的sum最大
      - 二分找到中间值 ok， 若是while中断，则比较right和right-1对应的sum

- 时间复杂度：`O(NlogN)`，由qsort限制的



## 代码实现

--------



分析知，数组最大和为：`10^4 * 10^5 = 10^9`，数组最小和为 `1`：故用int类型存储数组和结果不会溢出。策略都是先找到可行解，再找最优值。

代码版本如下：

```c
#define MAX_NUM_SIZE 10001

int Compar(void *a, void *b)
{
    return *(int *)a - *(int *)b; // ascending order
}

int GetIdx(int* arr, int arrSize, int target)
{
    int i;
    for (i = 0; i < arrSize; i++) {
        if (arr[i] > target) {
            return i;
        }
    }
    // todo
    // int left = 0;
    // int right = arr[arrSize - 1];
    // while (left < right) {
    //     int mid = left + ((right - left) >> 1);
    //     int curSum = mid;
    // }
    return -1; // not found
}

int findBestValue(int* arr, int arrSize, int target)
{
    // sort and get preSum
    qsort(arr, arrSize, sizeof(int), Compar);
    int preSum[MAX_NUM_SIZE] = {0};
    int i;
    for (i = 1; i < arrSize + 1; i++) {
        preSum[i] = arr[i - 1] + preSum[i - 1];
    }

    // process data
    int res = preSum[arrSize] - target;
    if (res <= 0) {
        return arr[arrSize - 1];
    }

    int curIdx, midSum, preeSum;
    int left = 0;
    int right = arr[arrSize - 1] + 1;
    while (left < right) {
        int mid = left + ((right - left) >> 1);
        curIdx = GetIdx(arr, arrSize, mid); // first idx which is greater than mid
        if (curIdx == -1) {
            res = mid;      // last time is ok, this time sum must greater than target
            break;          // so right is mid, and end while, compare right and right - 1
        }
        midSum = (arrSize - curIdx) * mid;
        preeSum = preSum[curIdx];
        int curSum = preeSum + midSum;
        int diff = curSum - target;
        if (diff == 0) {
            return mid;
        } else if (diff < 0) {
            left = mid + 1;
        } else if (diff > 0) {
            right = mid;
        }
    }

    // if left == right, then end while
    if (right <= 0) {
        return right;
    }

    int lSum, rSum, absDiffL, absDiffR;
    // break while contidon
    if (left != right && left > 0) { // compare res and left - 1
        rSum = preSum[arrSize];
    } else {  // normal end while condition
        curIdx = GetIdx(arr, arrSize, right);
        midSum = (arrSize - curIdx) * right;
        preeSum = preSum[curIdx];
        rSum = preeSum + midSum;
    }
    left = left - 1;
    curIdx = GetIdx(arr, arrSize, left);
    midSum = (arrSize - curIdx) * left;
    preeSum = preSum[curIdx];
    lSum = preeSum + midSum;

    absDiffL = abs(lSum - target);
    absDiffR = abs(rSum - target);
    return (absDiffL <= absDiffR) ? left : right;
}
```



附测试demo如下：

```c
int main(void)
{
    int ret;
    int arr[3] = {4,9,3};
    ret = findBestValue(arr, 3, 10); // 3
    printf("ret = %d\n", ret);

    int arr1[3] = {2,3,5};
    ret = findBestValue(arr1, 3, 10); // 5
    printf("ret = %d\n", ret);

    int arr2[5] = {60864,25176,27249,21296,20204};
    ret = findBestValue(arr2, 5, 56803); // 11361
    printf("ret = %d\n", ret);

    int arr3[3] = {1,1,1};
    ret = findBestValue(arr3, 3, 3); // 1
    printf("ret = %d\n", ret);

    int arr4[5] = {48772,52931,14253,32289,75263};
    ret = findBestValue(arr4, 5, 40876); // 8175
    printf("ret = %d\n", ret);

    int arr5[2] = {2,2};
    ret = findBestValue(arr5, 2, 3); // 1
    printf("ret = %d\n", ret);

    return 0;
}
```



大家**再想想**，代码还能再优化吗，还能更快更强些吗？

## 参考资料

------



1. [官方题解：1300. 转变数组后最接近目标值的数组和](https://leetcode-cn.com/problems/sum-of-mutated-array-closest-to-target/solution/)



