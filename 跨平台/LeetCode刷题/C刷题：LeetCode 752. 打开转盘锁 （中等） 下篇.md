
**目录**
[toc]
标题：C刷题：LeetCode 752. 打开转盘锁 （中等） 下篇


接上一篇博客：[C刷题：LeetCode 752. 打开转盘锁 （中等） || BFS广搜详解 （1）](https://blog.csdn.net/qq_17256689/article/details/118661633)


## Hash版本
---

**添加HASH数据结构**，熟悉uthash的使用，添加约束条件。

**主要约束条件**：
- 不走回头路，比如往前转动了一次的结果，不允许再往后转动回去
- 不能越过deadends
- 加上终止条件，一旦匹配到target就返回

**不走回头路**，比如往前转动了一次的结果，不允许再往后转动回去。如初始状态`0000`，转动一次变为`1000`，再转动一次不能转回去变为`0000`。实现的主要思路是，增加一个hash表`visit`记录所有已经有的遍历结果，如果出现相同的则跳过并且不记录。主要用uthash哈希表的O(1)查找功能，减少查找时间。

**不能越过deadends**，也是类似visit的hash表查找思路实现。hash表时间复杂度为O(1)，不过是以空间换时间，减少时间的同时会增长空间。

**终止条件**，由于该思路本身就是从转动次数0开始往外扩展的，类似一种贪心算法思想，每步都是最优的了。一旦匹配上目标，当前转动次数就是最小的，所以可以直接返回。


提交通过的hash版本代码如下：

```c
#define STR_SIZE 5
#define STR_LEN 4
int g_curLevelCnt;

typedef struct HashTable {
    char str[STR_SIZE]; // key
    UT_hash_handle hh; // table head
} StruHashTable;

typedef struct QueList {
    int cnt; // 转动次数
    char *s; // 当前密码
    struct QueList *next; // 下个可能密码
} StruQueList, *PtrStruQueList;

char* AddOne(char *in, int j)
{
    char *res = (char *)malloc(sizeof(char) * STR_SIZE);
    if (res == NULL) {
        return NULL;
    }
    memcpy(res, in, STR_SIZE);
    char ch = res[j];
    if (ch == '9') {
        res[j] = '0';
        return res;
    }
    res[j] = ch + 1;
    return res;
}

char* MinusOne(char *in, int j)
{
    char *s = (char *)malloc(sizeof(char) * STR_SIZE);
    if (s == NULL) {
        return NULL;
    }
    memcpy(s, in, STR_SIZE);
    char ch = s[j];
    if (ch == '0') {
        s[j] = '9';
        return s;
    }
    s[j] = ch - 1;
    return s;
}

void Init(StruQueList **pQue, char *s, int cnt)
{
    (*pQue) = (PtrStruQueList)malloc(sizeof(StruQueList));
    (*pQue)->cnt = cnt;
    char *str = (char *)malloc(sizeof(char) * STR_SIZE);
    if (str == NULL) {
        return;
    }
    memcpy(str, s, STR_SIZE);
    (*pQue)->s = str;
    (*pQue)->next = NULL;
    g_curLevelCnt++;
}

// 大于0，则表示匹配成功，返回转动次数
// 等于0，则表示无异常
// 小于0，则表示出错
int NodeExpand(StruQueList **queList, StruQueList **ptrQueListLastNode, char *target, StruHashTable **ptrDead, StruHashTable **ptrVisit)
{
    int i;
    char *s;
    StruHashTable *hashTmp1, *hashTmp2;

    // 转动1次, 当前节点演变出8种可能
    char *cur = (*queList)->s;
    int cnt = (*queList)->cnt;
    for (i = 0; i < 4; i++) {
        s = AddOne(cur, i);
        // 如果与target匹配
        if (strcmp(s, target) == 0) { // 终止条件
            return cnt + 1;
        }

        // 如果在deadends
        HASH_FIND(hh, *ptrDead, s, sizeof(char) * STR_SIZE, hashTmp1);
        // 如果已遍历
        HASH_FIND(hh, *ptrVisit, s, sizeof(char) * STR_SIZE, hashTmp2);
        if (hashTmp1 == NULL && hashTmp2 == NULL) { // 不在dead里也没在visit里
            Init(&(*ptrQueListLastNode)->next, s, cnt + 1);
            *ptrQueListLastNode = (*ptrQueListLastNode)->next;
            hashTmp1 = (StruHashTable *)malloc(sizeof(StruHashTable)); // 增加一个hash节点
            if (hashTmp1 == NULL) {
                return -1;
            }
            memcpy(hashTmp1->str, s, STR_SIZE);
            HASH_ADD(hh, *ptrVisit, str, sizeof(char) * STR_SIZE, hashTmp1);
            // printf("%s\n", s);
        } else {
            free(s);
        }

        s = MinusOne(cur, i);
        // 如果与target匹配
        if (strcmp(s, target) == 0) { // 终止条件
            return cnt + 1;
        }
        // 如果在deadends
        HASH_FIND(hh, *ptrDead, s, sizeof(char) * STR_SIZE, hashTmp1);
        // 如果已遍历
        HASH_FIND(hh, *ptrVisit, s, sizeof(char) * STR_SIZE, hashTmp2);
        if (hashTmp1 == NULL && hashTmp2 == NULL) { // 不在dead里也没在visit里
            Init(&(*ptrQueListLastNode)->next, s, cnt + 1);
            *ptrQueListLastNode = (*ptrQueListLastNode)->next;
            hashTmp1 = (StruHashTable *)malloc(sizeof(StruHashTable)); // 增加一个hash节点
            if (hashTmp1 == NULL) {
                return -1;
            }
            memcpy(hashTmp1->str, s, STR_SIZE);
            HASH_ADD(hh, *ptrVisit, str, sizeof(char) * STR_SIZE, hashTmp1);
            // printf("%s\n", s);
        } else {
            free(s);
        }
    }
    // printf("\n");
    return 0;
}

// 按队列和BFS的方法来表达每次只转一次，对应的所有可能
int openLock(char ** deadends, int deadendsSize, char * target)
{
    char cur[STR_SIZE] = "0000"; // 初始值
    int i, ret;

    // special case
    if (strcmp(cur, target) == 0) {
        return 0;
    }

    // 初始化hash
    StruHashTable *dead = NULL; // 表头最开始都为空
    StruHashTable *hashTmp;
    for (i = 0; i < deadendsSize; i++) {
        HASH_FIND(hh, dead, deadends[i], sizeof(char) * STR_SIZE, hashTmp); // 键值所占空间sizeof(char) * 5
        if (hashTmp == NULL) { // 之前未出现
            hashTmp = (StruHashTable *)malloc(sizeof(StruHashTable)); // 增加一个hash节点
            if (hashTmp == NULL) {
                return -1;
            }
            memcpy(hashTmp->str, deadends[i], STR_SIZE);
            HASH_ADD(hh, dead, str, sizeof(char) * STR_SIZE, hashTmp); // str表示操作结构体中的键值，追加到hashtable中
        }
    }

    // special case
    HASH_FIND(hh, dead, target, sizeof(char) * STR_SIZE, hashTmp);
    if (hashTmp != NULL) {
        return -1;  // deanends contain target
    }
    HASH_FIND(hh, dead, cur, sizeof(char) * STR_SIZE, hashTmp);
    if (hashTmp != NULL) {
        return -1;  // deanends contain target
    }

    // 初始化队列 0000
    StruHashTable *visit = NULL; // 表头最开始都为空
    PtrStruQueList queList, queListCurLevelLast, queListLastNode;
    queList = (PtrStruQueList)malloc(sizeof(StruQueList));
    if (queList == NULL) {
        return -1; // if malloc is failed
    }
    g_curLevelCnt = 0;
    Init(&queList, cur, 0);
    hashTmp = (StruHashTable *)malloc(sizeof(StruHashTable)); // 增加一个hash节点
    if (hashTmp == NULL) {
        return -1;
    }
    memcpy(hashTmp->str, queList->s, STR_SIZE);
    // printf("%p\n", visit);
    HASH_ADD(hh, visit, str, sizeof(char) * STR_SIZE, hashTmp); // 增加一个已遍历状态
    // printf("%p\n", visit);

    g_curLevelCnt = 0;
    queListLastNode = queList;
    ret = NodeExpand(&queList, &queListLastNode, target, &dead, &visit);
    if (ret > 0) { // 终止条件
        return ret;
    }
    queListCurLevelLast = queListLastNode;
    queList = queList->next;

    // 从所有层每个节点中迭代新的可能
    while (queList != NULL) {
        // 遍历当前层所有节点
        int len = g_curLevelCnt;
        g_curLevelCnt = 0;
        for (i = 0; i < len; i++) {
            ret = NodeExpand(&queList, &queListLastNode, target, &dead, &visit);
            if (ret > 0) { // 终止条件
                return ret;
            }
            queList = queList->next;
        }
    }

    return -1; // 遍历完所有无匹配
}
```

## Hash版本优化
---
**代码精简**：删除冗余信息和提炼重复代码为独立子函数。

 - 删除合并重复代码减少层序遍历冗余 
 - 将超50行的代码拆分成子功能函数
 - 替换魔鬼数字为有意义的宏命名
 - 优化while循环加lastNode判断为for循环，采用全局变量计数当前层个数作为len

主要变化的代码如下：
```c
void InitDeadHash(char **deadends, int deadendsSize, StruHashTable **ptrDead)
{
    int i;
    StruHashTable *hashTmp;
    for (i = 0; i < deadendsSize; i++) {
        HASH_FIND(hh, *ptrDead, deadends[i], sizeof(char) * STR_SIZE, hashTmp); // 键值所占空间sizeof(char) * 5
        if (hashTmp == NULL) { // 之前未出现
            hashTmp = (StruHashTable *)malloc(sizeof(StruHashTable)); // 增加一个hash节点
            if (hashTmp == NULL) {
                return;
            }
            memcpy(hashTmp->str, deadends[i], STR_SIZE);
            HASH_ADD(hh, *ptrDead, str, sizeof(char) * STR_SIZE, hashTmp); // str表示操作结构体中的键值，追加到hashtable中
        }
    }
    return;
}

void InitQueAndVisitHash(char *cur, StruQueList **ptrQueList, StruHashTable **ptrVisit)
{
    StruHashTable *hashTmp;
    *ptrQueList = (PtrStruQueList)malloc(sizeof(StruQueList));
    if (*ptrQueList == NULL) {
        return; // if malloc is failed
    }
    g_curLevelCnt = 0;
    Init(ptrQueList, cur, 0);
    hashTmp = (StruHashTable *)malloc(sizeof(StruHashTable)); // 增加一个hash节点
    if (hashTmp == NULL) {
        return;
    }
    memcpy(hashTmp->str, (*ptrQueList)->s, STR_SIZE);
    HASH_ADD(hh, *ptrVisit, str, sizeof(char) * STR_SIZE, hashTmp); // 增加一个已遍历状态
    return;
}

int DealCurStr(char *s, char *target, int cnt, StruQueList **ptrQueListLastNode, StruHashTable **ptrDead, StruHashTable **ptrVisit)
{
    StruHashTable *hashTmp1, *hashTmp2;
    // 如果与target匹配
    if (strcmp(s, target) == 0) { // 终止条件
        return cnt + 1;
    }

    // 如果在deadends
    HASH_FIND(hh, *ptrDead, s, sizeof(char) * STR_SIZE, hashTmp1);
    // 如果已遍历
    HASH_FIND(hh, *ptrVisit, s, sizeof(char) * STR_SIZE, hashTmp2);
    if (hashTmp1 == NULL && hashTmp2 == NULL) { // 不在dead里也没在visit里
        Init(&(*ptrQueListLastNode)->next, s, cnt + 1);
        *ptrQueListLastNode = (*ptrQueListLastNode)->next;
        hashTmp1 = (StruHashTable *)malloc(sizeof(StruHashTable)); // 增加一个hash节点
        if (hashTmp1 == NULL) {
            return -1;
        }
        memcpy(hashTmp1->str, s, STR_SIZE);
        HASH_ADD(hh, *ptrVisit, str, sizeof(char) * STR_SIZE, hashTmp1);
        // printf("%s\n", s);
    } else {
        free(s);
    }
    return 0;
}

// 大于0，则表示匹配成功，返回转动次数
// 等于0，则表示无异常
// 小于0，则表示出错
int NodeExpand(StruQueList *queList, StruQueList **ptrQueListLastNode, char *target, StruHashTable **ptrDead, StruHashTable **ptrVisit)
{
    int i, ret;
    char *s;

    // 转动1次, 当前节点演变出8种可能
    char *cur = queList->s;
    int cnt = queList->cnt;
    for (i = 0; i < 4; i++) {
        s = AddOne(cur, i);
        ret = DealCurStr(s, target, cnt, ptrQueListLastNode, ptrDead, ptrVisit);
        if (ret > 0) {
            return ret;
        }

        s = MinusOne(cur, i);
        ret = DealCurStr(s, target, cnt, ptrQueListLastNode, ptrDead, ptrVisit);
        if (ret > 0) {
            return ret;
        }
    }
    // printf("\n");
    return 0;
}

int LevelTraverse(StruQueList *queList, StruQueList **ptrQueListLastNode, char *target, StruHashTable **ptrDead, StruHashTable **ptrVisit)
{
    // 得到两个队列的指针，一个是当前指向，一个是层级对应的新开头
    // 从所有层每个节点中迭代新的可能
    int i, ret;
    while (queList != NULL) {
        // 遍历当前层所有节点
        int len = g_curLevelCnt;
        g_curLevelCnt = 0;
        for (i = 0; i < len; i++) {
            ret = NodeExpand(queList, ptrQueListLastNode, target, ptrDead, ptrVisit);
            if (ret > 0) { // 终止条件
                return ret;
            }
            queList = queList->next;
        }
        // printf("\n\n");
    }

    return 0;
}

// 按队列和BFS的方法来表达每次只转一次，对应的所有可能
int openLock(char ** deadends, int deadendsSize, char * target)
{
    char cur[STR_SIZE] = "0000"; // 初始值
    int ret;

    // special case
    if (strcmp(cur, target) == 0) {
        return 0;
    }

    // 初始化dead hash
    StruHashTable *dead = NULL; // 表头最开始都为空
    StruHashTable *hashTmp, *hashTmp1, *hashTmp2;
    InitDeadHash(deadends, deadendsSize, &dead);

    // special case
    HASH_FIND(hh, dead, target, sizeof(char) * STR_SIZE, hashTmp1);
    HASH_FIND(hh, dead, cur, sizeof(char) * STR_SIZE, hashTmp2);
    if (hashTmp1 != NULL || hashTmp2 != NULL) {
        return -1;  // deanends contain target
    }

    // 初始化队列0000和visit hash
    StruHashTable *visit = NULL; // 表头最开始都为空
    PtrStruQueList queList, queListLastNode;
    InitQueAndVisitHash(cur, &queList, &visit);

    queListLastNode = queList;
    ret = LevelTraverse(queList, &queListLastNode, target, &dead, &visit);
    if (ret > 0) { // 终止条件
        return ret;
    }
    return -1; // 遍历完所有无匹配
}
```

## 展望
----

 - 可参考官方解答写法，简化openLock()中循环代码实现
 - 可采用双向BFS进一步提高效率
 - 可采用启发式搜索`A*`算法进一步提高效率
