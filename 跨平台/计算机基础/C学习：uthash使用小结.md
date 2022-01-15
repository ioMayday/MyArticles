


**目录**


[toc]

标题：C学习：uthash使用小结


以LC题转盘锁为例，结合结构体/字符串匹配来讲解HASH表的建立、查找、增加、删除等。

## 基本概念
---

uthash是一个C语言的hash表实现的开源项目。它以宏定义的方式实现hash表，具有运行速度快、与关键类型无关等优点。uthash使用方便，只需将头文件uthash.h进行include即可使用。

## 实例讲解
---

### 定义Hash表结构
首先建立一个结构体，包含Key键值str字符串以及hash表头hh，hh定义模式固定，直接复制该句即可。
```c
typedef struct HashTable {
    char str[5]; // key
    UT_hash_handle hh; // table head
} StruHashTable;
```

### 初始化
将二维的字符串指针初始化到hash表中，同时应用了**查找**和**添加**的功能。
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
```

### 查找
 - 参数1为固定表头hh，不会变
 - 参数2为当前hash表结构的首地址，可以将hash表结构看成单个链表节点，此处输入当前节点头
 - 参数3为要查找的元素，输入为字符串首指针
 - 参数4为hash表键值的空间大小，单位byte
 - 参数5为hash表结构指针，存储查找的返回结果，若查到则非空，否则为空

```c
HASH_FIND(hh, *ptrDead, deadends[i], sizeof(char) * STR_SIZE, hashTmp); // 键值所占空间sizeof(char) * 5
```

注意：宏调用时会改变指针变量，所以`InitDeadHash()`最后一个参数要传二级指针进去，并在`HASH_FIND()`第二个参数里，用一级指针解引用来赋值。

### 增加
- 除参数3外，其他参数与查找功能相同
- 参数3为要添加的元素，此处为待添加的字符串首指针

```c
HASH_ADD(hh, *ptrDead, str, sizeof(char) * STR_SIZE, hashTmp); // str表示操作结构体中的键值，追加到hashtable中
```

### 删除
- `todo`
```c
HASH_DEL(*head, user);  /* user: pointer to deletee */
```

### 实际应用
以*LeetCode 752. 打开转盘锁*题目为例，实际应用的源代码如下：

```c
/*
// 先实现最简单的，找到target
// 再加约束deadends跳过 + 最小次数
// 实现子函数加减功能
*/

typedef struct HashTable {
    char str[5]; // key
    UT_hash_handle hh; // table head
} StruHashTable;

typedef struct QueList {
    int cnt; // 转动次数
    char *s; // 当前密码
    struct QueList *next; // 下个可能密码
} StruQueList, *PtrStruQueList;

#define STR_SIZE 5
#define STR_LEN 4
int g_curLevelCnt;

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


## 参考资料
---

1. [C语言Hash表使用方法详解](https://www.cnblogs.com/dongxb/p/14212531.html)
2. [C ：uthash](https://www.cnblogs.com/pam-sh/p/12827658.html)

