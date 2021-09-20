**目录**
[toc]

标题：C刷题：LeetCode 654. 最大二叉树 （中等）



## 思路一
----
本题关键在于考察对二叉树递归思路的写法。个人的思路实现，时间复杂度与方法二相同，但缺点在于代码较为冗余，不够精炼。**关键点**如下：

 - `节点功能`：每个节点的功能是找到最大值，赋值根节点数值
 - `遍历方式`：前序遍历，划分区间，继续递归创建左右子树
 - `停止条件`：左右索引越界
 - `切换条件`：左下标大于右侧下标，left > right
 - `返回值`：void
 - `注意`：该思路先有根节点再有子节点，类似于从上往下，本质还没有发挥递归魅力

实现代码如下；
```c
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     struct TreeNode *left;
 *     struct TreeNode *right;
 * };
 */

typedef struct treeNode {
     int val;
     struct TreeNode *left;
     struct TreeNode *right;
} TreeNode;

int FindMaxValIdx(int *nums, int left, int right)
{
    int maxVal = INT_MIN;
    int i, idx = 0;
    for (i = left; i <= right; i++) {
        if (nums[i] > maxVal) {
            maxVal = nums[i];
            idx = i;
        }
    }
    return idx;
}

void MaxBiTree(TreeNode* root, int *nums, int numsSize, int left, int right)
{
    if (left < 0 || right >= numsSize) { // 已递归到数组的边界处
        return; // debug发现，这句判断冗余，在后面处理了异常区间一般不会进入这句
                // 除非第一次调用时不符合索引要求
    }

    int idx = FindMaxValIdx(nums, left, right);
    root->val = nums[idx];
    printf("%d  ", root->val);

    if (idx - 1 < left) { // 出现左右区间异常不会进入递归
        root->left = NULL;
        printf("null ");
    } else {
        TreeNode* pLeft = (TreeNode*)malloc(sizeof(TreeNode));
        pLeft->val = 0;
        pLeft->left = NULL;
        pLeft->right = NULL;
        root->left = pLeft;
        MaxBiTree(root->left, nums, numsSize, left, idx - 1);
    }

    if (idx + 1 > right) { // 出现左右区间异常不会进入递归
        root->right = NULL;
        printf("null ");
        return;
    } 
    TreeNode* pRight = (TreeNode*)malloc(sizeof(TreeNode));
    pRight->val = 0;
    pRight->left = NULL;
    pRight->right = NULL;
    root->right = pRight;
    MaxBiTree(root->right, nums, numsSize, idx + 1, right);
    return;
}

struct TreeNode* constructMaximumBinaryTree(int* nums, int numsSize)
{
    TreeNode* root = (TreeNode*)malloc(sizeof(TreeNode));
    root->val = 0;
    root->left = NULL;
    root->right = NULL;

    MaxBiTree(root, nums, numsSize, 0, numsSize - 1);

    return root;
}
```


## 思路二
---
参考了labuladong的实现思路，更精炼，也是对方法一的一个代码优化，但不改变时间复杂度。**关键点**如下：

 - `节点功能`：每个节点的功能是找到最大值，赋值根节点
 - `遍历方式`：前序遍历，划分区间，继续递归创建左右子树，并将返回值赋予当前根节点的左右子节点
 - `停止条件`：左下标大于右侧下标，left > right，返回NULL
 - `返回值`：节点指针，左右子树的根节点
 - `注意`：该思路先有子节点再有根节点，类似于从下往上返回，真正发挥了递归魅力

实现代码如下；
```c
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     struct TreeNode *left;
 *     struct TreeNode *right;
 * };
 */

 // 闭区间 [left, right]
struct TreeNode* buildMaxTree(int* nums, int left, int right) 
{
    // base case
    if (left > right) return NULL;

    struct TreeNode* root = (struct TreeNode *)malloc(sizeof(struct TreeNode));
    // 找到最大值做当前的根节点
    int i, idx = left, maxVal = nums[left]; // 需同步初始化idx=left
    for (i = left + 1; i <= right; i++) {   // 否则会报内存越界 
        if (nums[i] > maxVal) {
            maxVal = nums[i];
            idx = i;
        }
    }
    root->val = maxVal;

    // 递归调用生成左右子树
    root->left = buildMaxTree(nums, left, idx - 1);
    root->right = buildMaxTree(nums, idx + 1, right);

    return root;
}

struct TreeNode* constructMaximumBinaryTree(int* nums, int numsSize)
{
    if (nums == NULL) return NULL;

    return buildMaxTree(nums, 0, numsSize - 1);
}
```

