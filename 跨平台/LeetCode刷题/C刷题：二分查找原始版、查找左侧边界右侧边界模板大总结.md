**目录**
[toc]

标题：C刷题：二分查找原始版、查找左侧边界/右侧边界模板大总结


## 引言
---
二分法常用在数组和查找场景，可以用`logN`的时间复杂度，查找到某值或者上下区间，效率较高，使用频繁，但又经常容易出错，故此总结用法供后面查阅回顾。

对边界值和不等情况时的下标移动，务必要理解透彻，并多练习检验。**如果不想关注具体代码实现细节，可以直接拉到末尾，看二分对比总结表格，一目了然。**

## 二分查找值
---

**常见区间条件**：
- 左闭右开：`[left, right)`，跳出条件一定为`left == right`
- 左闭右闭：`[left, right]`，跳出条件一定为`left > right`


下文中未明确说明的，使用的区间统一为左闭右开：`[left, right)`。

**自写代码版本功能**：

 - 查找升序数组中某值是否存在
 - 若存在，则返回其下标
 - 若不存在，则返回-1
 - 若某值在数组中存在多个，返回下标未定

代码实现如下：
```c
// 输入：升序数组，数组大小及待查找的目标
// 输出：若查找到，则返回对应下标；否则返回-1
int binarySearch(int *nums, int numsSize, int target)
{
    int left = 0;
    int right = numsSize;
    int mid;
    while (left < right) {
        mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            return mid; // 若找到
        } else if (nums[mid] < target) { // 左侧区间右移
            left = mid + 1;
        } else if (nums[mid] > target) { // 右侧区间左移
            right = mid;
        }
    }
    return -1; // 没找到，则返回-1
}
```

**库函数版本功能**：

 - 查找有序数组中某值是否存在
 - 若存在，则返回其值所在的元素指针
 - 若不存在，则返回NULL
 - 若某值在数组中存在多个，返回的值具体是哪个重复值不确定


函数签名：`void *bsearch(const void *_Key, const void *_Base, size_t _NumOfElements, size_t _SizeOfElements, int (*_PtFuncCompare)(const void *, const void *) __attribute__((cdecl)))`

头文件：`#include <stdlib.h>`

重要说明：

 - 输入数组可以为升序或降序，但最后一个compar函数必须与之对应
 - 输入的目标查找元素必须为其对应指针地址，若为常数，则会报错
 - 返回值为元素指针或者NULL
 - 目标值所在位置下标可通过返回值地址与首地址相减得到，前提是数组元素内存连续


**调用demo代码如下**：
```c
#include <stdlib.h>

int Compar(void *a, void *b)
{
    return *(int *)a - *(int *)b;
}

// 找有序数组的值
int main(void)
{
    int nums[10] = {0, 0, 2, 2, 4, 5, 6, 9, 9, 9};
    int numsSize = 10;
    int key = 6;
    int *ret = (int*)bsearch(&key, nums, numsSize, sizeof(int), Compar);

    if (ret != NULL) {
        printf("target == %d, index = %d\n", *ret, (int)(ret - &nums[0])); // 获得下标
    } else {
        printf("target not found\n");
    }

    return 0;
}
```

## 二分查找左边界
---
**文中左右边界指的是数组连续重复值的左右侧，为闭区间**。如 `int nums[10] = {0, 0, 2, 2, 4, 5, 6, 9, 9, 9};`，数组中目标值为9的范围是`[7, 9]`，即左侧边界下标为7， 右侧边界下标为9。

上一个版本的增强版，可以针对重复值，找到数组目标值出现的左边界。
```c
// 输入：升序数组，数组大小及待查找的目标，存在重复值
// 输出：若查找到，则返回出现该值的左侧边界对应下标；否则返回-1
int binarySearchLowerBound(int *nums, int numsSize, int target)
{
    int left = 0;
    int right = numsSize;
    int mid;
    int flag = 0; // 默认没找到
    while (left < right) {
        mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            right = mid; // 若找到，缩小右区间
            flag = 1;
        } else if (nums[mid] < target) { // 左侧区间右移
            left = mid + 1;
        } else if (nums[mid] > target) { // 右侧区间左移
            right = mid;
        }
    }
    if (flag == 1) { // 找到
        return left; // right = mid，left到达mid时跳出
    }
    return -1; // 没找到，则返回-1
}
```

## 二分查找右边界
---
代码实现如下：
```c
// 输入：升序数组，数组大小及待查找的目标，存在重复值
// 输出：若查找到，则返回出现该值的右侧边界对应下标；否则返回-1
int binarySearchUpperBound(int *nums, int numsSize, int target)
{
    int left = 0;
    int right = numsSize;
    int mid;
    int flag = 0; // 默认没找到
    while (left < right) {
        mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            left = mid + 1; // 若找到，缩小左区间
            flag = 1;
        } else if (nums[mid] < target) { // 左侧区间右移
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid; // 注意
        }
    }
    if (flag == 1) { // 找到
        return left - 1; // 因mid相等时，left = mid + 1
    }
    return -1; // 没找到，则返回-1
}
```


## 左右边界查找二合一版
---
查找左右边界的控制，靠bound入参决定，如`bound = 0`，则找左边界；如`bound = 1`，则找右边界。

**返回值分析**，找左边界，相等时不断缩小右边界，到左侧相等才跳出，所以返回`left`。找右边界，相等时不断缩小左边界，到右侧相等才跳出，右侧相等时的`right`是开区间取不到的，所以是`right - 1`。

另外可以发现，该函数稍作更改，可以实现查找在数组中重复出现的某值区间范围的功能，且输出范围为左闭右开，只需要把`bound = 1`时，返回值`right - 1`，改为`right`即可，也即返回第一个大于目标值的下标。

**代码实现**如下：

```c
// 输入：升序数组，数组大小及待查找的目标，存在重复值
// 输出：若查找到，则返回出现该值的右侧边界对应下标；否则返回-1
int binarySearchBound(int *nums, int numsSize, int target, int bound)
{
    int left = 0;
    int right = numsSize;

    while (left < right) {
        int mid = left + (right - left) / 2;
        int condition = (bound == 0) ? (nums[mid] < target) : (nums[mid] <= target); // 合并==
        if (condition) {
            left = mid + 1; // 左侧区间更新
        } else {            // 右侧区间更新
            right = mid;
        }
    }

    if (bound == 0 && nums[left] == target) { // 若是左侧边界
        return left;
    } 
    if (bound == 1 && nums[right - 1] == target) { // 若是右侧边界
        return right - 1; // = left - 1
    } 
    return -1;
}
```

## 二分查找总结
---

需要注意的是，不存在target的时候，直接返回-1。在二分查找值时，返回条件是`nums[mid] == target`时直接return,而查找左右侧边界时，返回条件则需要等`while()`循环完毕后，才能返回。观察下表可知，**区间右侧开闭主要影响right的更新和while判断**。

|          场景          |   左闭右开[left, right)    |     左闭右闭[left, right]      |   备注   |
| :--------------------: | :------------------------: | :----------------------------: | :------: |
|        初始赋值        | left = 0, right = numsSize | left = 0, right = numsSize - 1 | 部分不同 |
|       while条件        |        left < right        |         left <= right          |   不同   |
| `nums[mid] < target `  |       left = mid + 1       |         left = mid + 1         |   相同   |
| `nums[mid] > target `  |        right = mid         |        right = mid - 1         |   不同   |
| `nums[mid] == target ` |          返回mid           |            返回mid             |   相同   |



下面左右侧边界查找采用的是左闭右开区间，读者有兴趣可自行分析左闭右闭区间对应的情况。**注意**，如果有左边界不存在的场景，在while循环后，要判断下标对应值是否与target相等。

观察下表可知，在区间开闭情况相同时，**左右侧边界的查找的主要区别在于`nums[mid] == target`时边界更新和返回值**。

|         场景          |          左侧边界          |          右侧边界          | 备注 |
| :-------------------: | :------------------------: | :------------------------: | :--: |
|       初始赋值        | left = 0, right = numsSize | left = 0, right = numsSize | 相同 |
|       while条件       |        left < right        |        left < right        | 相同 |
| `nums[mid] < target `|       left = mid + 1       |       left = mid + 1       | 相同 |
| `nums[mid] > target ` |        right = mid         |        right = mid         | 相同 |
| `nums[mid] == target` |        right = mid         |       left = mid + 1       | 不同 |
|        返回值         |            left            |          left - 1          | 不同 |




## 实战练习
---
通过实战练习并能自己独立实现，方能检验是否理解到位，推荐以下练习：
 - 练习使用系统库函数
 - 读者可尝试自行实现闭区间的二分法查找
 - 总结闭区间对应的左右侧边界差异
 
 **附闭区间二分查找代码**实现供参考：
 
```c
int search(int* nums, int numsSize, int target){
    if (nums == NULL) return -1;
    int left = 0;
    int right = numsSize - 1; // 左闭右闭区间

    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        }
    }
    return -1; // 遍历完未找到
}
```

 **相关LeetCode习题：**

**`找值`**
 - [704.二分查找（简单）](https://leetcode-cn.com/problems/binary-search/)
 - [633. 平方数之和](https://leetcode-cn.com/problems/sum-of-square-numbers/)
 - [69. x 的平方根](https://leetcode-cn.com/problems/sqrtx/)
 - [剑指 Offer 53 - II. 0～n-1中缺失的数字](https://leetcode-cn.com/problems/que-shi-de-shu-zi-lcof/)


**`找左边界`**

 - [875.爱吃香蕉的珂珂（中等）](https://leetcode-cn.com/problems/koko-eating-bananas/)
 - [658. 找到 K 个最接近的元素](https://leetcode-cn.com/problems/find-k-closest-elements/)
 - [275. H 指数 II](https://leetcode-cn.com/problems/h-index-ii/)
 - [1300. 转变数组后最接近目标值的数组和](https://leetcode-cn.com/problems/sum-of-mutated-array-closest-to-target/)
 - [410. 分割数组的最大值](https://leetcode-cn.com/problems/split-array-largest-sum/)
 - [35. 搜索插入位置](https://leetcode-cn.com/problems/search-insert-position/)
 - [1011.在D天内送达包裹的能力（中等）](https://leetcode-cn.com/problems/capacity-to-ship-packages-within-d-days/)

**`找右边界`**

 - [1712. 将数组分成三个子数组的方案数](https://leetcode-cn.com/problems/ways-to-split-array-into-three-subarrays/)
 -  [1552.两球之间的磁力](https://leetcode-cn.com/problems/xiao-zhang-shua-ti-ji-hua/solution/)
 -  [34.在排序数组中查找元素的第一个和最后一个位置（中等）](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)
 - [经典砍树例子](https://oi-wiki.org/basic/binary/)

## 参考资料
---
1. [我写了首诗，让你闭着眼睛也能写对二分搜索](https://labuladong.gitbook.io/algo/mu-lu-ye/er-fen-cha-zhao-xiang-jie)
2. [二分搜索怎么用？我又总结了套路](https://labuladong.gitbook.io/algo/mu-lu-ye-1/mu-lu-ye-3/er-fen-yun-yong)
3. [我和快手面试官对二分搜索进行了深度探讨](https://labuladong.gitbook.io/algo/mu-lu-ye-1/mu-lu-ye-3/er-fen-fen-ge-zi-shu-zu)
4. [C 语言中用bsearch()实现查找操作](https://www.cnblogs.com/darklights/p/5022505.html)
5. [二分应用：【砍树例子很经典】](https://oi-wiki.org/basic/binary/)


