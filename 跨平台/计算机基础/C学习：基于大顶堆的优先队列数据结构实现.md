
**目录**
[toc]

标题：C学习：基于大顶堆的优先队列数据结构实现



## 功能分析
----
在一些场景中，需要这样一种数据结构：`优先队列`，功能不是先进先出，而是队前端一直是占权重最大的（如最大值或最小值），进入队列后依然要保持。每次弹入弹出都要保证队列单调性，即形成一个单调队列。

类似的功能也可以用`大顶堆/小顶堆`，采用完全二叉树数据结构来实现。不管是优先队列还是大顶堆，为便于理解，下文都只采用数组来进行模拟（不用链表指针）。数组到完全二叉树的映射关系是层序遍历。


## 函数拆分
---
 - init() 初始化数据结构，动态申请内存空间
 - setfree() 析构，释放动态分配的内存空间
 - push() 压入数据
 - pop() 弹出数据
 - top() 取顶端数据（优先队列最前面的，大顶堆最顶上的）


## 代码实现
----

**采用数组模拟时，统一的数据结构定义如下：**

```c
// Prior Quneue，完全用数组形式实现的最大堆功能
typedef struct MaxPriorQue {
    int** heap;  // 二维数组 heap[i][0/1] 0是idx,1是val
    int size;    // 当前队列大小
    int capcity; // 队列最大容量
} MaxPriorQueStru, *pMaxPriorQueStru;
```

**相同的初始化方法和释放空间的代码实现**：

`init()`函数
```c
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
```
`setfree()`函数
```c
void setfree(pMaxPriorQueStru p)
{
    int i;
    for (i = 0; i < p->capcity; i++) {
        free(p->heap[i]);
    }
    free(p->heap);
    free(p);
    return;
}
```

### 方法1
**基础版，采用数组来实现，调整时采用交换排序达到优先队列的效果。** 虽然易于理解和实现，但缺点是交换排序速度较慢。

`push()`函数：
```c
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
```

`pop()`函数：
```c
void pop(pMaxPriorQueStru p)
{
    p->size -= 1;
    return;
}
```
`top()`函数：
```c
int* top(pMaxPriorQueStru p)
{
    return p->heap[p->size - 1];
}
```

### 方法2
进阶版，用数组来模拟完全二叉树，可以更快的完成堆的调整。完全二叉树通过层序遍历存在数组中，故下标i经四则运算 `(i + 1) / 2`取整，可定位到i索引对应的二叉树节点，对应的上一层末尾节点。为便于二叉树节点计算，下标i经四则运算 `i / 2 `，需从数组的下标1开始放数据。所以要适当修改下init()函数，即多申请一个字节的空间。

`init()`函数
```c
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
```

`cmpValOrIdx()`辅助比较函数：

```c
int cmpValOrIdx(int *a, int *b)
{
    return a[1] == b[1] ? a[0] - b[0] : a[1] - b[1]; // 优先比较数值，再比较下标
}
```

`push()`函数：

```c
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
```

`pop()`函数：
```c

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
```
`top()`函数：
```c
int* top(pMaxPriorQueStru p)
{
    return p->heap[1]; // 为方便下标引用，索引0空置不用
}
```


## 参考资料
------

1. [优先队列push pop操作](https://blog.csdn.net/weixin_43784288/article/details/91058358)
2. [图解大顶堆的构建、排序过程](https://www.cnblogs.com/sunshineliulu/p/12995910.html)
3. 相关练习题：[C刷题：LeetCode 239. 滑动窗口最大值 （困难）](https://blog.csdn.net/qq_17256689/article/details/116741312)

