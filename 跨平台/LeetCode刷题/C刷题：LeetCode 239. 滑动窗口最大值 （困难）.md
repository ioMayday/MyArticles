# C刷题：LeetCode 239. 滑动窗口最大值 （困难）

**目录**
[toc]

**完整代码**见Git项目地址：[LeetCodeUsingC刷题笔记](https://github.com/ioMayday/leetcode_sol)

该题可以优化的点比较多，解决的思路也不少，故特此整理4种解法，并辅以个人在实现时的一些的经验总结，十分有助于开阔解题思路和加深认识。大部分疑难点已在注释里澄清，**若有问题，欢迎评论区讨论交流**。

## 基础知识
----

 - 高级数据结构：大顶堆
 - 两种实现：数组、二叉树
 - 大顶堆：数组到完全二叉树的映射关系是层序遍历，具体见参考资料3里的内容

## 实现0
----
自己的思路：用缓存当前窗口三个最值的方法，来降低移出窗的、进入窗的值对最值的影响。

问题在于：不能保证前三个最值，全部匹配当前窗口的前三个最值。当前三个最值挨着的时候，往后挪动三个，最值丢失，只能比较的是新进来的三个当最值，而不能把前面部分的数值再重新考虑进来。

```c
 // 此套逻辑废弃，有漏洞，不能覆盖所有情况
 // 因为不能保证，最大值连续移除时，最大值、次大值、第三大值能实时表示
 // 特别是从第三大值依次往上变为最大值时，不是真正的最大值

 /* 失败用例
[7157,9172,7262,-9146,3087,5117,4046,7726,-1071,6011,5444,-48,-1385,-7328,3255,1600,586,-5160,-371,-5978,9837,3255,-6137,8587,-3403,9775,260,6016,9797,3371,2395,6851,2349,-7019,9318,1211,-3110,8735,-7507,1784,7400,-5799,3169,-7696,-8991,-2222,-9434,-4490,4034,-831,-9656,5488,-4395,9339,4104,-9058,-4072,-1172,1758,6878,-5570,-6380,9550,-9389,1411,2298,3516,551,9196,5215,-237,-4146,1682,4418,-4639,7759,9593,-9588,3041,9208,-7331,-797,-2529,7738,-2944,4351,5091,-9448,-5404,6200,-1425,-3983,678,8456,-8085,5162,7165,4692,-494,-9249,8514,521,-8835,6745,-5775,-575,1876,-5464,5053,5567,3456,5873,1965,4316,2126,9462,-59,6544,-1547,7015,-8928,-3903,-3020,5865,-9479,6723,9214,5705,5136,7725,945,-1995,-2288,4579,7103,9938,4495,-730,-3180,7717,6824,794,-894,-1439,-1641,-4577,9362,-8817,-6035,-7980,-1278,-1928,-5390,-2342,1189,-2340,4788,-1814,5927,3115,9017,6801,7884,-5719,5992,7477,-486,-2734,-1557,3169,5288,-8295,-5651,2491,-3394,8302,-8822,5638,7654,7350,9884,-5392,881,-4874,5582,8309,-8514,2682...
45
 */

typedef struct SlidMaxNumInfo {
    int max;    // 第1大
    int secMax; // 第2大
    int thrMax; // 第3大
} SlidMaxNumInfoStru, *pSlidMaxNumInfoStru;

void UpdateInMaxValInfo(pSlidMaxNumInfoStru slidMaxInfo, int val)
{
    if (val >= slidMaxInfo->max) { // 取=是为了避免多个最大值
        slidMaxInfo->thrMax = slidMaxInfo->secMax;
        slidMaxInfo->secMax = slidMaxInfo->max;
        slidMaxInfo->max = val;
    } else if (val >= slidMaxInfo->secMax) {
        slidMaxInfo->thrMax = slidMaxInfo->secMax;
        slidMaxInfo->secMax = val;
    } else if (val >= slidMaxInfo->thrMax) {
        slidMaxInfo->thrMax = val;
    }
    return;
}

void UpdateOutMaxValInfo(pSlidMaxNumInfoStru slidMaxInfo, int val, int* maxOutCnt)
{
    if (val == slidMaxInfo->max) {
        slidMaxInfo->max = slidMaxInfo->secMax;
        slidMaxInfo->secMax = slidMaxInfo->thrMax;
        slidMaxInfo->thrMax= INT_MIN;
        (*maxOutCnt)++;
    } else if (val == slidMaxInfo->secMax) {
        slidMaxInfo->secMax = slidMaxInfo->thrMax;
        slidMaxInfo->thrMax= INT_MIN;
    } else if (val == slidMaxInfo->thrMax) {
        slidMaxInfo->thrMax= INT_MIN;
    }
    return;
}

int* maxSlidingWindow(int* nums, int numsSize, int k, int* returnSize)
{
    // 已保证输入 k < numSize
    // k = 1 直接输出数组

    if (k == 1) {
        *returnSize = numsSize;
        return nums;
    }

    // k != 1
    int resSize = numsSize - k + 1;
    *returnSize = resSize;
    SlidMaxNumInfoStru slidMaxInfo = {INT_MIN, INT_MIN, INT_MIN};
    int *resArr = (int *)malloc(*returnSize * sizeof(int));
    if (resArr == NULL) { return NULL; }
    int cnt = 0;
    
    int start = 0;
    int end = 0;
    int *p = resArr;

    // 初始化第一个窗口
    end = start;
    while (end < start + k) {
        UpdateInMaxValInfo(&slidMaxInfo, nums[end]);
        end++;
    }

    // 开始往后滑动
    *p = slidMaxInfo.max;
    p++;
    start++;
    while (start < resSize) {
        // 先匹配可行解，滑动到右侧，并找到最大值
    
        // 缩小范围，找到次大值

        // 更新当前窗口最大值，滑动窗口
        UpdateOutMaxValInfo(&slidMaxInfo, nums[start - 1], &cnt);
        if (cnt == 2) {
            end = start;
            while (end < start + k) {
                UpdateInMaxValInfo(&slidMaxInfo, nums[end]);
                end++;
            }
            cnt = 0;
            end--;
        } else {
            UpdateInMaxValInfo(&slidMaxInfo, nums[end]);
        }
        
        *p = slidMaxInfo.max;
        p++;

        // 滑动
        start++;
        end++;
    }

    return resArr;
}
```

## 实现1
---
参考LeetCode官方题解思路，自己独立实现后可以通过，但是由于完全用选择排序数组来实现的最大堆，面对大数据时，会超出时间限制。

优化点：将push和pop从O(N)优化到 O(logN)，不要相邻的交换复制。采用完全二叉树的表示，但依然用数组的结构，由于顶端都是最大值，就可以用二分法来调整堆的结构。

```c
/**
 * Note: The returned array must be malloced, assume caller calls free().
 */

// Prior Quneue，完全用数组形式实现的最大堆功能
typedef struct MaxPriorQue {
    int** heap;  // 二维数组 heap[i][0/1] 0是idx,1是val
    int size;    // 当前队列大小
    int capcity; // 队列最大容量
} MaxPriorQueStru, *pMaxPriorQueStru;

void init(pMaxPriorQueStru p, int size)
{
    p->size = 0;
    p->heap = (int**)malloc(size * sizeof(int*));
    // if (p->heap == NULL) { return NULL;}
    p->capcity = size;

    int i;
    for (i = 0; i < p->capcity; i++) {
        p->heap[i] = (int *)malloc(2 * sizeof(int));
        // if (p->heap[i] == NULL) { return NULL; }
    }

    return;
}

// 完全用数组形式实现的最大堆功能
void push(pMaxPriorQueStru p, int idx, int val)
{
    // 已保证堆足够大，不用担心溢出
    int top = p->size++;

    // 调整堆
    // 先复制交换二级指针存储的地址，找到插入位后再赋值
    int i = top - 1;
    //int *tmp = p->heap[top];
    while ((i >= 0) && (p->heap[i][1] > val)) {
        p->heap[i + 1][0] = p->heap[i][0];
        p->heap[i + 1][1] = p->heap[i][1];
        i--;
    }
    if (i < 0) {
        // p->heap[0] = tmp;
        p->heap[0][0] = idx;
        p->heap[0][1] = val;
    } else {
        // p->heap[i + 1] = tmp;
        p->heap[i + 1][0] = idx;
        p->heap[i + 1][1] = val;
    }

    return;
}

void pop(pMaxPriorQueStru p)
{
    p->size = p->size - 1;
    return;
}

int* top(pMaxPriorQueStru p)
{
    return p->heap[p->size - 1];
}

void SetFree(pMaxPriorQueStru p)
{
    int i;
    for (i = 0; i < p->capcity; i++) {
        free(p->heap[i]);
    }
    free(p->heap);
    free(p);
    return;
}

// 利用最大堆队列数据结构来完成
int* maxSlidingWindow(int* nums, int numsSize, int k, int* returnSize)
{
    if (k == 1) {
        *returnSize = numsSize;
        return nums;
    }

    // 压入前k个数据
    pMaxPriorQueStru q = (pMaxPriorQueStru)malloc(sizeof(MaxPriorQueStru));
    if (q == NULL) { return NULL; }
    init(q, numsSize);

    int resSize = numsSize - k + 1;
    int *resArr = (int *)malloc(resSize * sizeof(int));
    if (resArr == NULL) { return NULL; }

    int end = 0;
    int i;
    for (i = 0; i < k; i++) {
        push(q, i, nums[i]);
    }
    int *heap = top(q);
    // resArr[*returnSize++] = heap[1]; // 作用是：resArr[*returnSize], 再returnSize++，加在地址上了，与预期不符
    resArr[end++] = heap[1];
    //printf("%d \n", *returnSize);
    //printf("%d ", heap[1]);

    // 从k往后移动
    for (i = k; i < numsSize; i++) {
        push(q, i, nums[i]);
        heap = top(q);
        while (heap[0] <= i - k) {
            pop(q);
            heap = top(q);
        }
        //resArr[*returnSize++] = heap[1];
        resArr[end++] = heap[1];
        // printf("%d \n", *returnSize);
        // printf("%d ", heap[1]);
    }
    //printf("%d \n", *returnSize);

    *returnSize = end;
    SetFree(q);

    return resArr;
}
```

## 实现2
----
改进实现1中的`pop()`和`push()`等相关函数。首先分析理解官方题解用意，详细注释如下：
```c
void swap(int** a, int** b) {
    int* tmp = *a;
    *a = *b, *b = tmp;
}

int cmp(int* a, int* b) {
	 // 优先比较数值，其次再比较索引，索引大的在窗右侧更新，放上一层
    return a[0] == b[0] ? a[1] - b[1] : a[0] - b[0];
}

void push(struct Heap* obj, int num0, int num1) {
    int sub1 = ++(obj->size), sub2 = sub1 >> 1; 
    (obj->heap[sub1])[0] = num0, (obj->heap[sub1])[1] = num1; // 把新插入的值放到堆的末尾
    // 找二叉树上一层最右侧的叶子节点比较
    while (sub2 > 0 && cmp(obj->heap[sub2], obj->heap[sub1]) < 0) { 
     	// 若上一层sub2叶子节点 < 新插入的sub1叶子节点，则交换位置
        swap(&(obj->heap[sub1]), &(obj->heap[sub2]));
        sub1 = sub2, sub2 = sub1 >> 1; // 继续往上一层比较
    }
    // 到达顶端或者新插入的值比上一层叶子节点小已找到对应位置
}

void pop(struct Heap* obj) {
    int sub = 1;
    // 待删除的顶端位置与末端节点交换，然后减小
    swap(&(obj->heap[sub]), &(obj->heap[(obj->size)--])); 
    while (sub <= obj->size) { // 从上往下调整最大堆
    	 // sub1为左孩子索引，sub2=sub1+1为右孩子索引
        int sub1 = sub << 1, sub2 = sub << 1 | 1;
        int maxSub = sub; // 假设当前父节点是最大值
        if (sub1 <= obj->size && cmp(obj->heap[maxSub], obj->heap[sub1]) < 0) { // 左孩子比maxSub节点大，交换
            maxSub = sub1;
        }
        if (sub2 <= obj->size && cmp(obj->heap[maxSub], obj->heap[sub2]) < 0) { // 右孩子比maxSub节点大，交换
            maxSub = sub2;
        }
        if (sub == maxSub) { // 无交换，父节点已最大，则堆已调整完毕，不需要再往下调整
            break;
        }
        swap(&(obj->heap[sub]), &(obj->heap[maxSub])); // 如果有交换，则交换指针位置
        sub = maxSub; // 交换后，下沉到交换后的索引位置
    }
    // break跳出或者已调整到末端节点退出
}
```

**将以下修改点对应的代码替代实现1中对应部分**，即可提交通过：

 - 增加比较函数`cmpValOrIdx()`
 - 修改`init()`
 - 修改`pop()`
 - 修改`push()`
 - 修改`top()`

**注意点：**
为方便运算，0索引位置空出来不存储，上一层的末尾叶节点下标索引为当前插入值下标索引除以2。

```c
int cmpValOrIdx(int *a, int *b)
{
    return a[1] == b[1] ? a[0] - b[0] : a[1] - b[1]; // 优先比较数值，再比较下标
}

void init(pMaxPriorQueStru p, int size)
{
    p->size = 0;
    p->heap = (int**)malloc((size + 1) * sizeof(int*));
    // if (p->heap == NULL) { return NULL;}
    p->capcity = size + 1;

    int i;
    for (i = 0; i < p->capcity; i++) {
        p->heap[i] = (int *)malloc(2 * sizeof(int));
        // if (p->heap[i] == NULL) { return NULL; }
    }

    return;
}

// 完全用数组形式实现的最大堆功能
void push(pMaxPriorQueStru p, int idx, int val)
{
    // 已保证堆足够大，不用担心溢出
    p->size += 1;

    // 先放末端节点
    p->heap[p->size][0] = idx;
    p->heap[p->size][1] = val;

    // 通过与上层节点不断比较交换，找到合适位置
    int i0 = p->size >> 1;
    int i1 = p->size;
    while (i0 > 0 && cmpValOrIdx(p->heap[i0], p->heap[i1]) < 0) {
        int *tmp = p->heap[i0]; // 交换二级指针指向的一级指针地址
        p->heap[i0] = p->heap[i1];
        p->heap[i1] = tmp;
        i1 = i0; // 继续往上一层末端节点比较
        i0 >>= 1;
    }

    return;
}

void pop(pMaxPriorQueStru p)
{
    // 将顶端的值与末尾节点交换，再自减容量
    int *tmp = p->heap[1]; // 交换二级指针指向的一级指针地址
    p->heap[1] = p->heap[p->size];
    p->heap[p->size] = tmp;
    p->size--;

    // 调整堆，把末尾节点下沉到合适位置
    int i = 1; // 从顶端开始
    while (i <= p->size) {
        int l = i << 1; // 左孩子索引
        int r = l + 1; // 右孩子索引
        int max = i;
        if (l <= p->size && cmpValOrIdx(p->heap[max], p->heap[l]) < 0) {
            max = l;
        }
        if (r <= p->size && cmpValOrIdx(p->heap[max], p->heap[r]) < 0) {
            max = r;
        }
        if (max == i) { // 如果父节点就已是最大值，则调整完毕，顶端交换的值已找到合适位置
            break;
        }
        // 需要交换
        int *tmp = p->heap[i]; // 交换二级指针指向的一级指针地址
        p->heap[i] = p->heap[max];
        p->heap[max] = tmp;

        i = max; // 和max的指针交换后，当前max索引是被交换到顶端的底部值，待进一步下沉比较
    }
    // i下沉到最末端或者末尾节点已下沉到合适位置，结束调整

    return;
}

int* top(pMaxPriorQueStru p)
{
    return p->heap[1]; // 为方便下标引用，索引0空置不用
}
```

## 实现3
---
参考LeetCode官方题解方法二思路，继续优化，将**实现0**的思路改进成单调队列，不仅只存3个最值，而是动态存储更新当前窗的最值，依次单调递减。

**思路核心点**：

 - 申请一个与输入数组同样大小的数组`maxQueIdx`，作为单调队列
 - 队列存储的只是输入数组对应元素的索引
 - 单调队列的数组`maxQueIdx`下标范围`[left, right)`
 - 队首`left`始终是当前窗口最大值的索引，`right`指向队尾元素的下一个位置，队列从左到右单调递减
 - 队列更新：
 	- 比较移入窗口的元素，若比队尾元素小，则直接入队尾；否则，队尾元素出队`right--`，直到比队尾元素小，再入队；（更新最值）
 	- 判断当前队首元素索引是否在当前窗内，不在的话需要队首出队`left++`（说明当前队列最大值刚被移出窗了）

独立实现代码如下：

```c
int* maxSlidingWindow(int* nums, int numsSize, int k, int* returnSize)
{
    if (k == 1) {
        *returnSize = numsSize;
        return nums;
    }
    // 单调队列思路，从左到右递减；思路类同第一个保存第一第二个最大值思路
    int maxQueIdx[numsSize];
    int resSize = numsSize - k + 1;
    int *resArr = (int *)malloc(resSize * sizeof(int));
    if (resArr == NULL) { return NULL; }
    *returnSize = resSize;

    int i;
    int left = 0;
    int right = 0;
    for (i = 0; i < k; i++) {
        while (left < right && nums[i] >= nums[maxQueIdx[right - 1]]) {
            right--; // 出队列
        }
        maxQueIdx[right++] = i;
    }
    resSize = 0;
    resArr[resSize++] =  nums[maxQueIdx[left]];

    for (i = k; i < numsSize; i++) {
        // 若窗口加入的新元素比队尾元素大于等于，则队尾元素出队列 
        // 之所以等于也要出队列，是因为下标idx最新，不容易移除窗口
        while (left < right && nums[i] >= nums[maxQueIdx[right - 1]]) { // 当前队尾元素为right - 1,队尾指针在right
            right--; // 出队列
        }
        maxQueIdx[right++] = i;

        while (maxQueIdx[left] <= i - k) { // 最大值不在窗外
            left++; // 弹出队首元素，左移
        }
        resArr[resSize++] =  nums[maxQueIdx[left]];
    }

    return resArr;
}
```

## 参考资料
---
1. [优先队列push pop操作](https://blog.csdn.net/weixin_43784288/article/details/91058358)
2. [LeetCode官方题解：滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/solution/hua-dong-chuang-kou-zui-da-zhi-by-leetco-ki6m/)
3. [图解大顶堆的构建、排序过程](https://www.cnblogs.com/sunshineliulu/p/12995910.html)


