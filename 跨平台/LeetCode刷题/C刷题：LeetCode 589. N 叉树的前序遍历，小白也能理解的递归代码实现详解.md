`@[toc](Cˢ�⣺LeetCode 589. N ������ǰ�������С��Ҳ�����ĵݹ����ʵ�����)`

**Ŀ¼**
[toc]

LeetCode 589. N ������ǰ���������Ŀ������[���˴�](https://leetcode-cn.com/problems/n-ary-tree-preorder-traversal/)��
## ����˼·
---
1. ��������Ŀ�����룬��Ŀ�Ѹ��ݲ������뽨���˶����
2. �ӽڵ�ṹ�嶨���У�����ÿ���ڵ㶼�и�����ָ��`children`�洢����һ���ӽڵ��ָ�����飬`numChildren`��¼�²��ӽڵ����
3. �����������������ͬ������������ı���ģ�弴�ɣ���`left/right`������������Ϊ`for`ѭ������`children`ָ������

## ����ʵ��
---
```c
/**
 * Definition for a Node.
 * struct Node {
 *     int val;
 *     int numChildren;
 *     struct Node** children;
 * };
 */
/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
#define MAX_SIZE    10000

void PreTraversal(struct Node *root, int *nums, int *pcnt)
{
    // ��ֹ����
    if (root == NULL) {
        return;
    }

    // ǰ�����
    nums[*pcnt] = root->val; // �����������
    (*pcnt)++;

    // ����
    int i;
    for (i = 0; i < root->numChildren; i++) {
        PreTraversal(root->children[i], nums, pcnt);
    }

    return;
}

int *preorder(struct Node *root, int *returnSize)
{
    *returnSize = 0;
    int *nums = (int*)malloc(MAX_SIZE * sizeof(int));
    if (nums == NULL) {
        return NULL;
    }
    int cnt = 0;
    PreTraversal(root, nums, &cnt);
    *returnSize = cnt;
    return nums;
}
```

## ��������
---

 - ˼���±���ĵ�����ʵ�֣� 
 - ˼������δӲ����������룬����������������
## �ο�����
---
1. [LeetCode��⣺��������C���Եݹ�](https://leetcode-cn.com/problems/n-ary-tree-preorder-traversal/solution/rong-yi-li-jie-de-cyu-yan-di-gui-by-sleeeeep/)



