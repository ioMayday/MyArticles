**目录**
[toc]

标题：C刷题：LeetCode 46. 全排列 （中等） || 回溯算法/DFS深搜类

本文涉及LeetCode类似题目：

>  47. 全排列 II
>  51. N 皇后 （困难）


## 回溯法/DFS深搜C语言模板
----

```c
void backtrack(输入参数)
{
	// baseCase终止条件
	if (满足终止条件) {
		将记录的结果存放到输出变量里；
		return;
	}
	
	// 递归调用
	for (遍历当前层所有节点) {
		处理节点，如把节点放入track数组
		backtrack(节点信息，track信息)
		返回节点，撤销track前面的记录，往上回溯
	}
	
	return;
}

输出参数 func(输入参数)
{
	// 前置处理与内存空间动态申请
	...
	
	// 回溯或深搜递归实现
	backtrack(输入/输出)
	
	return 输出参数
}
```

下面举个具体例子。

## 全排列思路
----
跳过审题，分析知就是一个穷举，关键是如何用合适的数据结构和算法实现，具体思路如下：

 - 首先，按题目要求动态分配内存空间
 - 其次，实现回溯递归算法：
	 - **a**. 确认终止条件为到叶节点，也即track记录长度等于numsSize
	 - **b**. 通过for循环遍历每层所有节点
	 - **c**. 经过节点时，记录当前节点，放入track数组
	 - **d**. 递归调用，继续往当前节点下一层搜索
	 - **e**. 返回节点时，弹出track数组中当前节点，往上回溯

## 代码实现
---

```c
// 判断元素是否已被遍历
int isContain(int *nums, int len, int val)
{
    int flag = 0;
    int i;
    for (i = 0; i < len; i++) {
        if (nums[i] == val) {
            flag = 1;
            break;
        }
    }
    return flag;
}

// 注意该全局变量最好是只声明定义，初始化放在backtrack前
// 以免LeetCode判题机没有重新初始化导致误判
int g_trackNum = 0; // 用于递归调用时临时入栈用
int g_rowPos = 0; // 记录每行

// 套用backtrack模板
void backtrack(int *nums, int numsSize, int **returnColumnSizes, int *track)
{
    // 到达叶节点track加入returnColumSizes，记录的路径已经等于数组长度停止
    int i;
    if (g_trackNum == numsSize) {
        // printf("back: g_rowPos = %d\n", g_rowPos);
        for (i = 0; i < numsSize; i++) {
            // printf("back: g_rowPos = %d\n", g_rowPos);
            returnColumnSizes[g_rowPos][i] = track[i];
        }
        g_rowPos++;
        return;
    }

    // 递归遍历
    for (i = 0; i < numsSize; i++) {
        // 确认当前值是否在track里
        if (isContain(track, g_trackNum, nums[i])) {
            continue;
        }

        // 不在的话，加入track
        // printf("back: g_trackNum = %d\n", g_trackNum);
        track[g_trackNum++] = nums[i];
        
        // 继续向后遍历
        backtrack(nums, numsSize, returnColumnSizes, track);
        // 节点返回后，取出track中的值
        g_trackNum--;
    }

    return;
}

// 套用func模板
int** permute(int* nums, int numsSize, int* returnSize, int** returnColumnSizes)
{
    // 计算所有可能的总数n!
    int row = 1, i;
    for (i = numsSize; i > 0; i--) {
        row *= i;
    }
    *returnSize = row;

    // printf("row = %d\n", row);

    // 计算返回数组中每行的列数
    *returnColumnSizes = (int *)malloc(sizeof(int) * (*returnSize));
    if (returnColumnSizes == NULL) {
        return NULL;
    }
    for (int i = 0; i < row; i++) {
        // (*returnColumnSizes)[i] = numsSize;
        // 等价于上条语句，returnColumnSizes[0]将二级指针解引用为一级指针，[i]解引用到对应的val所在地方完成赋值
        returnColumnSizes[0][i] = numsSize;
    }

    // 申请对应大小的二维数组并分配空间
    int **res = (int **)malloc((row + 10) * sizeof(int*));
    if (res == NULL) {
        return NULL;
    }
    int *p;
    for (i = 0; i < row; i++) {
        p = (int*)malloc((numsSize + 10) * sizeof(int));
        if (p == NULL) {
            return NULL;
        }
        res[i] = p;
    }
    p = (int*)malloc(numsSize * sizeof(int));
    if (p == NULL) {
        return NULL;
    }
    int *track = p;

    // 回溯穷举所有可能排列
    g_trackNum = 0;
    g_rowPos = 0;
    backtrack(nums, numsSize, res, track); // 从0行开始放结果

    return res;
}
```

分析代码可知，仍有以下`待优化点`：

 - 27行，for循环赋值可用`memcpy()`替代，减少代码量
 - 38行，`isContain()`函数可以用交换的形式来实现，降低时间复杂度

有兴趣的读者可自行尝试优化。


## 附：CleanCode
----
去掉debug代码和冗余注释后的代码：
```c
// 判断元素是否已被遍历
int isContain(int *nums, int len, int val)
{
    int flag = 0;
    int i;
    for (i = 0; i < len; i++) {
        if (nums[i] == val) {
            flag = 1;
            break;
        }
    }
    return flag;
}

// 注意该全局变量最好是只声明定义，初始化放在backtrack前
// 以免LeetCode判题机没有重新初始化导致误判
int g_trackNum; // 用于递归调用时临时入栈用
int g_rowPos; // 记录每行

void backtrack(int *nums, int numsSize, int **returnColumnSizes, int *track)
{
    // 到达叶节点track加入returnColumSizes，记录的路径已经等于数组长度停止
    int i;
    if (g_trackNum == numsSize) {
        for (i = 0; i < numsSize; i++) {
            returnColumnSizes[g_rowPos][i] = track[i];
        }
        g_rowPos++;
        return;
    }

    // 递归遍历
    for (i = 0; i < numsSize; i++) {
        // 确认当前值是否在track里
        if (isContain(track, g_trackNum, nums[i])) {
            continue;
        }

        // 不在的话，加入track
        track[g_trackNum++] = nums[i];
        // 继续向后遍历
        backtrack(nums, numsSize, returnColumnSizes, track);
        // 节点返回后，取出track中的值
        g_trackNum--;
    }

    return;
}

int** permute(int* nums, int numsSize, int* returnSize, int** returnColumnSizes)
{
    // 计算所有可能的总数n!
    int row = 1, i;
    for (i = numsSize; i > 0; i--) {
        row *= i;
    }
    *returnSize = row;

    // 计算返回数组中每行的列数
    *returnColumnSizes = (int *)malloc(sizeof(int) * (*returnSize));
    if (returnColumnSizes == NULL) {
        return NULL;
    }
    for (int i = 0; i < row; i++) {
        returnColumnSizes[0][i] = numsSize;
    }

    // 申请对应大小的二维数组并分配空间
    int **res = (int **)malloc((row + 10) * sizeof(int*));
    if (res == NULL) {
        return NULL;
    }
    int *p;
    for (i = 0; i < row; i++) {
        p = (int*)malloc((numsSize + 10) * sizeof(int));
        if (p == NULL) {
            return NULL;
        }
        res[i] = p;
    }
    p = (int*)malloc(numsSize * sizeof(int));
    if (p == NULL) {
        return NULL;
    }
    int *track = p;

    // 回溯穷举所有可能排列
    g_trackNum = 0;
    g_rowPos = 0;
    backtrack(nums, numsSize, res, track); // 从0行开始放结果

    return res;
}
```

## 参考资料
---

 1. labuladong的算法小抄
 2. [小镇做题家全排列C语言实现思路](https://leetcode-cn.com/problems/permutations/solution/46-quan-pai-lie-by-ac_fun-peev/)


