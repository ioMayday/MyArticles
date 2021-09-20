**目录**
[toc]

标题：C刷题：LeetCode 二叉树前序、中序、后序遍历


本文涉及LeetCode相关题目：

>  1. 94 二叉树的中序遍历
>  2. 145 二叉树的后序遍历
>  3. 144 二叉树的前序遍历

## 递归方法
---
递归写法较为简单，模板如下：

```c
void Traver(struct TreeNode* root, int *res, int* returnSize)
{
    if (root == NULL) { return; } // 终止条件

	// res[(*returnSize)++] = root->val;  // 前序
    Traver(root->left, res, returnSize);
    // res[(*returnSize)++] = root->val;  // 中序
    Traver(root->right, res, returnSize);
	// res[(*returnSize)++] = root->val;  // 后序
	
    return;
}
```

按前序、后序、中序不同要求，调整对根节点的处理语句位置即可。下文附上**以前序为例**的示例代码：

```c
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     struct TreeNode *left;
 *     struct TreeNode *right;
 * };
 */

/**
 * Note: The returned array must be malloced, assume caller calls free().
 */

// 前序遍历递归写法
#define MAX_NODE_NUM 101

void preorderTraverRecur(struct TreeNode* root, int* returnSize, int *res)
{
    if (root == NULL) { return; }

    res[(*returnSize)++] = root->val;
    preorderTraverRecur(root->left, returnSize, res);
    preorderTraverRecur(root->right, returnSize, res);
    return;
}

int* preorderTraversal(struct TreeNode* root, int* returnSize)
{
    *returnSize = 0;
    int *resArr = (int*)malloc(MAX_NODE_NUM * sizeof(int));
    if (resArr == NULL) { return NULL; }
    preorderTraverRecur(root, returnSize, resArr);
    return resArr;
}
```


## 迭代方法
---
迭代法较递归稍难些，本质是用一个栈来记录递归函数的出入栈过程，模仿人工遍历过程，步骤如下：
1. 先左侧节点到最深处，然后压栈记录中间遍历的节点
2. 到叶子节点后返回，并出栈打印对应数据，得到中序结果
3. 访问右侧节点，继续类似循环调用左侧节点
### 中序
中序输出顺序是`左根右`，详细代码如下：
```c
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     struct TreeNode *left;
 *     struct TreeNode *right;
 * };
 */

/**
 * Note: The returned array must be malloced, assume caller calls free().
 */

#define MAX_NODE_NUM 101

int* inorderTraversal(struct TreeNode* root, int* returnSize)
{
    int i = 0;
    int top = 0; // top记录着栈中元素个数
    int *resArr = (int*)malloc(MAX_NODE_NUM * sizeof(int));
    struct TreeNode** stkNodeArr = (struct TreeNode**)malloc(MAX_NODE_NUM * sizeof(struct TreeNode*));

    while (root != NULL || top > 0) { // 结束条件为到末尾节点底部，且遍历节点栈中都已出栈
        while (root != NULL) { // 先根节点左侧子树
            stkNodeArr[top++] = root; // 入栈记录过程节点
            root = root->left;
        } // 到达左侧叶节点的NULL

        // 取出栈顶节点，也即叶节点
        root = stkNodeArr[--top];
        resArr[i++] = root->val;
        // 叶节点右侧子树
        root = root->right;
    }

    *returnSize = i;
    free(stkNodeArr);

    return resArr;
}
```

### 前序
前序输出顺序是`根左右`，前序遍历迭代写法，相对于中序遍历的迭代方法，只需要调整打印输出语句的位置即可。具体见下方代码：
```c
#define MAX_NODE_NUM 101

int* preorderTraversal(struct TreeNode* root, int* returnSize)
{
    int i = 0;
    int top = 0; // top记录着栈中元素个数
    int *resArr = (int*)malloc(MAX_NODE_NUM * sizeof(int));
    struct TreeNode** stkNodeArr = (struct TreeNode**)malloc(MAX_NODE_NUM * sizeof(struct TreeNode*));

    while (root != NULL || top > 0) { // 结束条件为到末尾节点底部，且遍历节点栈中都已出栈
        while (root != NULL) { // 先根节点左侧子树
            stkNodeArr[top++] = root; // 入栈记录过程节点
            resArr[i++] = root->val; // 入栈时就输出记录，相当于前序遍历
            root = root->left;
        } // 到达左侧叶节点的NULL

        // 取出栈顶节点，也即叶节点
        root = stkNodeArr[--top];
        // 叶节点右侧子树
        root = root->right;
    }

    *returnSize = i;
    free(stkNodeArr);

    return resArr;
}
```

### 后序
后序输出顺序是`左右根`，总体思路不变，但后序时代码要做下调整，先判断是否满足输出时的**两种情况**：一种是当前根节点的右节点为空，一种是从右节点返回到上一个根节点进行输出。**若不满足**，由于是后序遍历当前节点不能出栈，继续遍历当前节点的右节点。

详细代码如下：

```c
#define MAX_NODE_NUM 101

int* postorderTraversal(struct TreeNode* root, int* returnSize)
{
    int i = 0;
    int top = 0; // top记录着栈中元素个数
    int *resArr = (int*)malloc(MAX_NODE_NUM * sizeof(int));
    struct TreeNode** stkNodeArr = (struct TreeNode**)malloc(MAX_NODE_NUM * sizeof(struct TreeNode*));
    struct TreeNode* pre = NULL;

    while (root != NULL || top > 0) { // 结束条件为到末尾节点底部，且遍历节点栈中都已出栈
        while (root != NULL) { // 先根节点左侧子树
            stkNodeArr[top++] = root; // 入栈记录过程节点
            root = root->left;
        } // 到达左侧叶节点的NULL

        // 取栈顶节点，也即叶节点
        root = stkNodeArr[--top];
        printf("%d ", root->val);
        // right 为NULL时，说明右子树已经遍历完
        // right 为pre时，说明逆序到根节点处，右子树已输出，待输出根节点
        if (root->right == NULL || root->right == pre) { // 后序输出  
            resArr[i++] = root->val;
            pre = root; // 后序输出的情况，输出root根1时，上一个输出的必然是当前根的右节点
            root = NULL; // 输出后root必须赋值空，以便继续出栈判断
        } else { // root->right != NULL && root->right != pre
            // 否则继续读取叶节点右侧子树,并压栈
            stkNodeArr[top++] = root; // 如果不能输出，则又把root压栈回去
            root = root->right;
        }
    }

    *returnSize = i;
    free(stkNodeArr);

    return resArr;  
}
```

## 参考资料
----
[LeetCode官方题解：二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/solution/er-cha-shu-de-zhong-xu-bian-li-by-leetcode-solutio/)