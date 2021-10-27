
**文章目录**
[toc]

标题：C刷题：LeetCode 297. 二叉树的序列化与反序列化  二叉树系列 | 困难

> 作者：来知晓
> 公众号：来知晓
> 刷题交流QQ群：444172041

## 题目描述

------

> **核心说明**：
>
> 请设计一个算法来实现二叉树的序列化与反序列化。这里不限定你的序列 / 反序列化算法执行逻辑，你只需要保证一个二叉树可以被序列化为一个字符串并且将这个字符串反序列化为原始的树结构。
>
> 提示: 输入输出格式与 LeetCode 目前使用的方式一致，详情请参阅 LeetCode 序列化二叉树的格式。你并非必须采取这种方式，你也可以采用其他的方法解决这个问题。
>
> **题目链接**：[297. 二叉树的序列化与反序列化](https://leetcode-cn.com/problems/sum-of-mutated-array-closest-to-target/)



## 思路分析

--------



认真阅读，理解题意，整理如下：

- 由于遍历形式不定，故可以用：前序、后序、层序遍历方法分别实现
- 注意，由于中序无法一步找到root，无法实现反序列化
- 题目例子输入是按层序遍历存的，可以自己设计存储方式，先从最简单的前序遍历开始实现
- 除题目已给用例外，设计特殊用例：左右单叉树，1，2，NULL，NULL，3，NULL，NULL


可得到两条思路，如下：

**思路1：前序遍历**

- 序列化：即用字符串存储前序遍历的结果
- 反序列化：从字符串中利用前序遍历赋值给树
- 时间复杂度：`O(N)`，因为访问了所有节点

**思路2：后序遍历**

- 序列化：即用字符串存储后序遍历的结果
- 反序列化：从字符串从后往前，先右子树再左子树，利用前序遍历赋值给树
- 时间复杂度：`O(N)`

**思路3：层序遍历**

- 序列化：将树的节点按层序遍历压平到队列中，队列节点中存的是数值和下个节点指针
- 反序列化：将对应数值按层序遍历的方式恢复节点的左右子树
- 层序遍历核心4步：
  - 1）建立队列数据结构，存储层序遍历顺序的节点地址、层深度（可选）
  - 2）while循环进行队列遍历，直到队列的节点内容为空
  - 3）不断压入root的左右子树，追加到队列末尾
  - 4）陆续读取队列节点的对应值，即可得到层序遍历输出
  - 技巧：获取队列头和队列尾的指针，实时更新
- 注意，若节点中数值为`-10000`，表示字符串中的`#`，当前节点为空节点
- 时间复杂度：`O(NlogN)`

**思路4：地址强转**

- 取巧实现，将root地址强制转成`char*`
- 再从序列恢复中，强制转为二叉树节点地址

## 代码实现

--------

### 思路1：前序遍历

- debug：结果不对

  - 分模块确认问题

  - 先分析具体报错点，再往前分析数据流

    - 转`str`后输出异常
  
    - 发现是`malloc`后的`str`，没有初始化为0，导致追加时，末尾是随机的
  
      ```c
      char s[BUF_STR_SIZE] = {0};
      sprintf(s, "%d,", val);
      strcat(strRes, s);
      ```
  
    - 还有返回的`str`已经被更改了，要在上层先做备份
  
- debug：提交反馈异常
  - 报错1：提示stack-overflow
  - 分析：动态申请的字符串内存和树的动态内存，使用后没释放
  - 报错2：`AddressSanitizer: stac-buffer-overflow in vsprintf`
  - 分析：发现是size给小了，把6改成10即可

前序实现代码如下：

```c
// 前序遍历方法

// # represent for NULL in string
#define STEP_NULL       2
#define BUF_STR_SIZE    10 // 设6会报错，题目的范围有问题，实际值会小于-1000的，可能会到-10000
#define MAX_LEN         10001

void Tree2StrPreOrderTraversal(struct TreeNode* root, char ** str)
{
    // corner case
    char *strRes = *str;
    if (root == NULL) {
        sprintf(strRes, "%s,", "#");
        *str += STEP_NULL;
        return;
    }

    // preorder process
    int val = root->val;
    char s[BUF_STR_SIZE] = {0};
    sprintf(s, "%d,", val);
    strcat(strRes, s);
    *str += strlen(s);

    // traversal
    Tree2StrPreOrderTraversal(root->left, str);
    Tree2StrPreOrderTraversal(root->right, str);
    return;
}

// return root
struct TreeNode* Str2TreePreOrderTraversal(char **str)
{
    // process string
    char valStr[BUF_STR_SIZE] = {0};
    char *strRes = *str;
    char *nextStr = NULL;
    const char *split = ",";
    strRes = strtok_r(strRes, split, &nextStr);
    if (strRes == NULL) {
        printf("strRes wrong!\n");
        return NULL;
    }
    *str = nextStr;
    strcpy(valStr, strRes);

    // corner case
    if (strlen(valStr) == 1 && valStr[0] == '#') {
        return NULL;
    }

    // preorder process
    struct TreeNode* root = (struct TreeNode*)malloc(sizeof(struct TreeNode));
    if (root == NULL) {
        return NULL;
    }
    sscanf(valStr, "%d", &root->val);

    // traversal
    root->left = Str2TreePreOrderTraversal(str);
    root->right = Str2TreePreOrderTraversal(str);
    return root;
}

/** Encodes a tree to a single string. */
char* serialize(struct TreeNode* root) {
    char *data = (char *)malloc(sizeof(char) * MAX_LEN * BUF_STR_SIZE);
    char *headData = data;
    memset(data, 0, MAX_LEN * BUF_STR_SIZE);
    if (data == NULL) {
        return NULL;
    }
    Tree2StrPreOrderTraversal(root, &data);
    return headData;
}

/** Decodes your encoded data to tree. */
struct TreeNode* deserialize(char* data) {
    char *headData = data;
    struct TreeNode* root = Str2TreePreOrderTraversal(&data);
    free(headData);
    return root;
}
```

### 思路2：后序遍历

- 代码难点是反序列化
- 核心是从后往前遍历，先建立根，再递归建立左右子树
- 左右子树顺序须为：先给right子树，再给left子树
- 本质仍是前序遍历

后序代码实现如下：

```c
// 后序遍历方法

// # represent for NULL in string
#define STEP_NULL       2
#define BUF_STR_SIZE    10 // 设6会报错，题目的范围有问题，实际值极可能小于-1000，会到-10000
#define MAX_LEN         20001  // 10001会报错内存越界，主要是反序列化时的valStr问题，暂时不设置成动态分配的

void Tree2StrPostOrderTraversal(struct TreeNode* root, char ** str)
{
    // corner case
    char *strRes = *str;
    if (root == NULL) {
        sprintf(strRes, "%s,", "#");
        *str += STEP_NULL;
        return;
    }

    // traversal
    Tree2StrPostOrderTraversal(root->left, str);
    Tree2StrPostOrderTraversal(root->right, str);

    // postorder process
    int val = root->val;
    char s[BUF_STR_SIZE] = {0};
    sprintf(s, "%d,", val);
    strcat(strRes, s);
    *str += strlen(s);
    return;
}

// return root
struct TreeNode* Str2TreePostOrderTraversal(char str[MAX_LEN][BUF_STR_SIZE], int* l)
{
    // corner case
    int len = *l;
    if (len == 0) {
        return NULL;
    }
    if (strlen(str[len - 1]) == 1 && str[len - 1][0] == '#') {
        return NULL;
    }
    
    // preorder traversal
    struct TreeNode* root = (struct TreeNode*)malloc(sizeof(struct TreeNode));
    if (root == NULL) {
        printf("malloc root wrong\n");
        return NULL;
    }
    sscanf(str[len - 1], "%d", &root->val);

    // traversal right tree first
    *l = *l - 1;
    root->right = Str2TreePostOrderTraversal(str, l);
    *l = *l - 1;
    root->left = Str2TreePostOrderTraversal(str, l);

    return root;
}

/** Encodes a tree to a single string. */
char* serialize(struct TreeNode* root) {
    char *data = (char *)malloc(sizeof(char) * MAX_LEN * BUF_STR_SIZE);
    char *headData = data;
    memset(data, 0, MAX_LEN * BUF_STR_SIZE);
    if (data == NULL) {
        return NULL;
    }
    Tree2StrPostOrderTraversal(root, &data);
    return headData;
}

/** Decodes your encoded data to tree. */
struct TreeNode* deserialize(char* data) {
    char *headData = data;

    // pre process string
    char valStr[MAX_LEN][BUF_STR_SIZE] = {0};
    char *strRes = data;
    char *nextStr = NULL;
    const char *split = ",";
    strRes = strtok_r(strRes, split, &nextStr);
    int len = 0;
    while (strRes != NULL) {
        strcpy(valStr[len++], strRes);
        strRes = strtok_r(NULL, split, &nextStr);
    }

    struct TreeNode* root = Str2TreePostOrderTraversal(valStr, &len);
    free(headData);
    return root;
}
```



### 思路3：层序遍历

- 序列化和反序列化时都要用到队列，来存储扁平后的数组
- 当前实现为：直接用全局变量大数组来模拟队列，大力出奇迹，减少动态分配malloc繁杂操作
- 待改进：可以只用一个队列链表，实现动态分配，结构体内容为：node指针，val值，next队列节点
- 时间复杂度：`O(N)`

```c
// 层序遍历方法

// -10000 represent for NULL in array
#define MAX_LEN         20001
#define VAL_NULL        (-10000)
#define END_FLAG        (-10001)
#define END_FLAG_INT    (-269488145)

typedef struct QueueNode {
    struct TreeNode *node;
} QueueStru, *PtrQueueStru;

QueueStru g_queList[MAX_LEN];
int g_levelRes[MAX_LEN];

int Tree2StrLevelOrderTraversal(struct TreeNode* root)
{
    if (root == NULL) {
        g_queList[0].node = NULL;
        g_levelRes[0] = END_FLAG;
        return 0;
    }

    // level traversal
    memset(g_queList, 0, sizeof(g_queList));
    memset(g_levelRes, END_FLAG, sizeof(g_levelRes));
    PtrQueueStru queHead = g_queList;
    PtrQueueStru queLast = g_queList + 1;
    g_queList[0].node = root;
    int len = 0;
    g_levelRes[len++] = root->val;

    while (queHead->node != NULL) {
        if (queHead->node->left == NULL) {
            g_levelRes[len++] = VAL_NULL;
        } else {
            queLast->node = queHead->node->left;
            g_levelRes[len++] = queLast->node->val;
            queLast++;
        }

        if (queHead->node->right == NULL) {
            g_levelRes[len++] = VAL_NULL;
        } else {
            queLast->node = queHead->node->right;
            g_levelRes[len++] = queLast->node->val;
            queLast++;
        }

        queHead++;
    }
    return len;
}

struct TreeNode* NewNode(void) {
    struct TreeNode* root = (struct TreeNode*)malloc(sizeof(struct TreeNode));
    if (root == NULL) {
        printf("malloc new node wrong\n");
        return NULL;
    }
    return root;
}

// return root
struct TreeNode* Str2TreePreOrderTraversal(int len)
{
    // corner case
    if (len == 0) {
        return NULL;
    }

    // init
    memset(g_queList, 0, sizeof(g_queList));
    PtrQueueStru queHead = g_queList;
    PtrQueueStru queLast = g_queList + 1;

    // level traversal
    struct TreeNode* root = NewNode();
    queHead->node = root;
    memset(queHead->node, 0, sizeof(struct TreeNode));
    int i = 0;
    queHead->node->val = g_levelRes[i++];
    while (i < len) {
        if (g_levelRes[i] != VAL_NULL && g_levelRes[i] != END_FLAG_INT) {
            queHead->node->left = NewNode();
            memset(queHead->node->left, 0, sizeof(struct TreeNode));
            queHead->node->left->val = g_levelRes[i];
            queLast->node = queHead->node->left;
            queLast++;
        } else {
            queHead->node->left = NULL;
        }
        i++;
        if (g_levelRes[i] != VAL_NULL && g_levelRes[i] != END_FLAG_INT) {
            queHead->node->right = NewNode();
            memset(queHead->node->right, 0, sizeof(struct TreeNode));
            queHead->node->right->val = g_levelRes[i];
            queLast->node = queHead->node->right;
            queLast++;
        } else {
            queHead->node->right = NULL;
        }
        i++;
        queHead++;
    }

    return root;
}

/** Encodes a tree to a single string. */
char* serialize(struct TreeNode* root) {
    int len = Tree2StrLevelOrderTraversal(root);
    return (char *)len;
}

/** Decodes your encoded data to tree. */
struct TreeNode* deserialize(char* data) {
    int len = (int)data;
    return Str2TreePreOrderTraversal(len);
}

```



### 思路4：地址强转取巧

```c
char* serialize(struct TreeNode* root) {
    return  (char *)root;
}

struct TreeNode* deserialize(char* data) {
    return (struct TreeNode *)data;
}
```



## 测试demo

---

附本地测试demo主调代码如下：

```c
struct TreeNode {
    int val;
    struct TreeNode *left;
    struct TreeNode *right;
};

// place your function
// ...

// demo test
void main(void)
{
    //  test case 1
    // struct TreeNode root, left, right;
    // root.val = 1;
    // root.left = &left;
    // root.right = &right;

    // left.val = 2;
    // left.left = NULL;
    // left.right = NULL;

    // right.val = 3;
    // right.left = NULL;
    // right.right = NULL;

    //  test case 2
    //  [-1000,null,-1000]
    struct TreeNode root, right;
    root.val = -1000;
    root.left = NULL;
    root.right = &right;

    right.val = -1000;
    right.left = NULL;
    right.right = NULL;

    char *str = serialize(&root);
    struct TreeNode *rootRecover = deserialize(str);

    return;
}
```



## 参考资料

------

1. [297. 二叉树的序列化与反序列化](https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/)

2. [层序遍历思路，可参考LeetCode网友题解](https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/solution/csan-chong-jie-fa-fu-zhu-shi-by-zai-xia-dan-shen-w/)

3. [二叉树的序列化，就那几个框架，枯燥至极](https://mp.weixin.qq.com/s/DVX2A1ha4xSecEXLxW_UsA)

