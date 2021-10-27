
**����Ŀ¼**
[toc]

���⣺Cˢ�⣺LeetCode 297. �����������л��뷴���л�  ������ϵ�� | ����

> ���ߣ���֪��
> ���ںţ���֪��
> ˢ�⽻��QQȺ��444172041

## ��Ŀ����

------

> **����˵��**��
>
> �����һ���㷨��ʵ�ֶ����������л��뷴���л������ﲻ�޶�������� / �����л��㷨ִ���߼�����ֻ��Ҫ��֤һ�����������Ա����л�Ϊһ���ַ������ҽ�����ַ��������л�Ϊԭʼ�����ṹ��
>
> ��ʾ: ���������ʽ�� LeetCode Ŀǰʹ�õķ�ʽһ�£���������� LeetCode ���л��������ĸ�ʽ���㲢�Ǳ����ȡ���ַ�ʽ����Ҳ���Բ��������ķ������������⡣
>
> **��Ŀ����**��[297. �����������л��뷴���л�](https://leetcode-cn.com/problems/sum-of-mutated-array-closest-to-target/)



## ˼·����

--------



�����Ķ���������⣬�������£�

- ���ڱ�����ʽ�������ʿ����ã�ǰ�򡢺��򡢲�����������ֱ�ʵ��
- ע�⣬���������޷�һ���ҵ�root���޷�ʵ�ַ����л�
- ��Ŀ���������ǰ����������ģ������Լ���ƴ洢��ʽ���ȴ���򵥵�ǰ�������ʼʵ��
- ����Ŀ�Ѹ������⣬����������������ҵ�������1��2��NULL��NULL��3��NULL��NULL


�ɵõ�����˼·�����£�

**˼·1��ǰ�����**

- ���л��������ַ����洢ǰ������Ľ��
- �����л������ַ���������ǰ�������ֵ����
- ʱ�临�Ӷȣ�`O(N)`����Ϊ���������нڵ�

**˼·2���������**

- ���л��������ַ����洢��������Ľ��
- �����л������ַ����Ӻ���ǰ������������������������ǰ�������ֵ����
- ʱ�临�Ӷȣ�`O(N)`

**˼·3���������**

- ���л��������Ľڵ㰴�������ѹƽ�������У����нڵ��д������ֵ���¸��ڵ�ָ��
- �����л�������Ӧ��ֵ����������ķ�ʽ�ָ��ڵ����������
- �����������4����
  - 1�������������ݽṹ���洢�������˳��Ľڵ��ַ������ȣ���ѡ��
  - 2��whileѭ�����ж��б�����ֱ�����еĽڵ�����Ϊ��
  - 3������ѹ��root������������׷�ӵ�����ĩβ
  - 4��½����ȡ���нڵ�Ķ�Ӧֵ�����ɵõ�����������
  - ���ɣ���ȡ����ͷ�Ͷ���β��ָ�룬ʵʱ����
- ע�⣬���ڵ�����ֵΪ`-10000`����ʾ�ַ����е�`#`����ǰ�ڵ�Ϊ�սڵ�
- ʱ�临�Ӷȣ�`O(NlogN)`

**˼·4����ַǿת**

- ȡ��ʵ�֣���root��ַǿ��ת��`char*`
- �ٴ����лָ��У�ǿ��תΪ�������ڵ��ַ

## ����ʵ��

--------

### ˼·1��ǰ�����

- debug���������

  - ��ģ��ȷ������

  - �ȷ������屨��㣬����ǰ����������

    - ת`str`������쳣
  
    - ������`malloc`���`str`��û�г�ʼ��Ϊ0������׷��ʱ��ĩβ�������
  
      ```c
      char s[BUF_STR_SIZE] = {0};
      sprintf(s, "%d,", val);
      strcat(strRes, s);
      ```
  
    - ���з��ص�`str`�Ѿ��������ˣ�Ҫ���ϲ���������
  
- debug���ύ�����쳣
  - ����1����ʾstack-overflow
  - ��������̬������ַ����ڴ�����Ķ�̬�ڴ棬ʹ�ú�û�ͷ�
  - ����2��`AddressSanitizer: stac-buffer-overflow in vsprintf`
  - ������������size��С�ˣ���6�ĳ�10����

ǰ��ʵ�ִ������£�

```c
// ǰ���������

// # represent for NULL in string
#define STEP_NULL       2
#define BUF_STR_SIZE    10 // ��6�ᱨ����Ŀ�ķ�Χ�����⣬ʵ��ֵ��С��-1000�ģ����ܻᵽ-10000
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

### ˼·2���������

- �����ѵ��Ƿ����л�
- �����ǴӺ���ǰ�������Ƚ��������ٵݹ齨����������
- ��������˳����Ϊ���ȸ�right�������ٸ�left����
- ��������ǰ�����

�������ʵ�����£�

```c
// �����������

// # represent for NULL in string
#define STEP_NULL       2
#define BUF_STR_SIZE    10 // ��6�ᱨ����Ŀ�ķ�Χ�����⣬ʵ��ֵ������С��-1000���ᵽ-10000
#define MAX_LEN         20001  // 10001�ᱨ���ڴ�Խ�磬��Ҫ�Ƿ����л�ʱ��valStr���⣬��ʱ�����óɶ�̬�����

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



### ˼·3���������

- ���л��ͷ����л�ʱ��Ҫ�õ����У����洢��ƽ�������
- ��ǰʵ��Ϊ��ֱ����ȫ�ֱ�����������ģ����У��������漣�����ٶ�̬����malloc���Ӳ���
- ���Ľ�������ֻ��һ����������ʵ�ֶ�̬���䣬�ṹ������Ϊ��nodeָ�룬valֵ��next���нڵ�
- ʱ�临�Ӷȣ�`O(N)`

```c
// �����������

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



### ˼·4����ַǿתȡ��

```c
char* serialize(struct TreeNode* root) {
    return  (char *)root;
}

struct TreeNode* deserialize(char* data) {
    return (struct TreeNode *)data;
}
```



## ����demo

---

�����ز���demo�����������£�

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



## �ο�����

------

1. [297. �����������л��뷴���л�](https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/)

2. [�������˼·���ɲο�LeetCode�������](https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/solution/csan-chong-jie-fa-fu-zhu-shi-by-zai-xia-dan-shen-w/)

3. [�����������л������Ǽ�����ܣ���������](https://mp.weixin.qq.com/s/DVX2A1ha4xSecEXLxW_UsA)

