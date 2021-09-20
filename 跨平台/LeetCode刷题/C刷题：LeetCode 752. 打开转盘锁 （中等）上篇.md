**目录**
[toc]

标题：C刷题：LeetCode 752. 打开转盘锁 （中等）



> PS：
> 你以为的中等题，结果官方解答用了`A*`算法。没有现成的数据结构，用C语言实现，不亚于难题，哈哈怪不得连官方答案`A*`算法实现里都懒得写C版本了。。。
> 所以，我就不搞那么复杂啦，就用BFS一步步深入，并把分析的思路记录于此吧。

## 思路分析
---
 - 先实现最简单的，遍历穷举所有可能
 - 子函数往前往后转动的功能
 - 接着实现BFS遍历找到target 
 - 再加约束deadends跳过 + 最小次数 


## 原始版本
---
独立实现一个遍历功能，思路是按一个位转动不同次数，然后第二个位转不同次数，往深处递归，本质是DFS遍历，代码如下：
```c
char AddOne(char ch)
{
    char ans;
    if (ch == '9') {
        return '0';
    }
    ans = ch + 1;
    return ans;
}

char MinusOne(char ch)
{
    char ans;
    if (ch == '0') {
        return '9';
    }
    ans = ch - 1;
    return ans;
}

int g_times;


void BfsFindMinTimes(char *cur, char *target, int depth)
{
    // 终止条件
    if (depth == 4) {
        return;
    }

    // 迭代搜索
    char ch1 = *cur;
    char ch2 = *target;
    while (ch1 != ch2) {
        int tmp = ch1 - ch2;
        tmp = abs(tmp);
        if (tmp > 5) {
            g_times += 10 - tmp; // need to rever dir
            tmp = 10 - tmp;
            while (tmp--) {
                ch1 = MinusOne(ch1);
            }   
        } else {
            g_times += tmp;
            while (tmp--) {
                ch1 = AddOne(ch1);
            }
        }
    }
    *cur = *target;
    BfsFindMinTimes(cur + 1, target + 1, depth + 1);

    return;
}

int openLock(char ** deadends, int deadendsSize, char * target)
{
    g_times = 0;
    char cur[5] = "0000"; // 初始值
    // special case
    if (strcmp(cur, target) == 0) {
        return 0;
    }
    int i;
    for (i = 0; i < deadendsSize; i++) {
        if (strcmp(cur, deadends[i]) == 0) {
            return -1;
        }
    }	
    BfsFindMinTimes(cur, target, 0);
    return g_times;
}
```


## 版本一：BFS穷举所有可能
---
由于原始版本思路泛化能力较弱，参考labuladong的思路，真正从BFS入手，**穷举所有可能**。代码如下：

```c
char* AddOne(char *in, int j)
{
    char *res = (char *)malloc(sizeof(char) * 5);
    if (res == NULL) {
        return NULL;
    }
    memcpy(res, in, 5);
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
    char *s = (char *)malloc(sizeof(char) * 5);
    if (s == NULL) {
        return NULL;
    }
    memcpy(s, in, 5);
    char ch = s[j];
    if (ch == '0') {
        s[j] = '9';
        return s;
    }
    s[j] = ch - 1;
    return s;
}


typedef struct QueList {
    int cnt; // 转动次数
    char *s; // 当前密码
    struct QueList *next; // 下个可能密码
} StruQueList, *PtrStruQueList;

void Init(StruQueList **pQue, char *s, int cnt)
{
    (*pQue) = (PtrStruQueList)malloc(sizeof(StruQueList));
    (*pQue)->cnt = cnt;
    char *str = (char *)malloc(sizeof(char) * (strlen(s) + 1));
    if (str == NULL) {
        return;
    }
    memcpy(str, s, strlen(s) + 1);
    (*pQue)->s = str;
    (*pQue)->next = NULL;
}

PtrStruQueList NodeExpand(StruQueList **queList, PtrStruQueList queListLastNode)
{
    int i;
    char *s;
    // 临时转动1次, 当前节点演变出8种可能
    char *cur = (*queList)->s;
    int cnt = (*queList)->cnt;
    for (i = 0; i < 4; i++) {
        s = AddOne(cur, i);
        printf("%s\n", s);
        Init(&queListLastNode->next, s, cnt + 1);
        queListLastNode = queListLastNode->next;

        s = MinusOne(cur, i);
        printf("%s\n", s);
        Init(&queListLastNode->next, s, cnt + 1);
        queListLastNode = queListLastNode->next;
    }
    printf("\n");
    return queListLastNode;
}

// 按队列和BFS的方法来表达每次只转一次，对应的所有可能
void BfsFindMinTimes(char *cur, char *target)
{
    // 初始化队列 0000
    PtrStruQueList queList, queListCurLevelLast, queListLastNode;
    queList = (PtrStruQueList)malloc(sizeof(StruQueList));
    if (queList == NULL) {
        return; // if malloc is failed
    }
    Init(&queList, cur, 0);
    queListCurLevelLast = queList;
    queListLastNode = NodeExpand(&queList, queListCurLevelLast);
    queListCurLevelLast = queListLastNode;
    queList = queList->next;

    // 得到两个队列的指针，一个是当前指向，一个是层级对应的新开头
    // 从所有层每个节点中迭代新的可能
    while (queList != NULL) {
        queListCurLevelLast = queListLastNode;
        // 遍历当前层所有节点
        while (queList != queListCurLevelLast) {
            queListLastNode = NodeExpand(&queList, queListLastNode);
            queList = queList->next;
        }
        queListLastNode = NodeExpand(&queList, queListLastNode); // 当前层最后一个节点
        queList = queList->next;
        printf("\n\n");
    }

    return;
}

// BFS
int openLock(char ** deadends, int deadendsSize, char * target)
{
    char cur[5] = "0000"; // 初始值

    // special case
    if (strcmp(cur, target) == 0) {
        return 0;
    }
    int i;
    for (i = 0; i < deadendsSize; i++) {
        if (strcmp(cur, deadends[i]) == 0) {
            return -1;
        }
    }

    BfsFindMinTimes(cur, target);

    return 1;
}
```
## 版本二：找到target即终止
---
接着继续改进，加上终止条件，实现找到target就停止遍历。
```c
char* AddOne(char *in, int j)
{
    char *res = (char *)malloc(sizeof(char) * 5);
    if (res == NULL) {
        return NULL;
    }
    memcpy(res, in, 5);
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
    char *s = (char *)malloc(sizeof(char) * 5);
    if (s == NULL) {
        return NULL;
    }
    memcpy(s, in, 5);
    char ch = s[j];
    if (ch == '0') {
        s[j] = '9';
        return s;
    }
    s[j] = ch - 1;
    return s;
}

typedef struct QueList {
    int cnt; // 转动次数
    char *s; // 当前密码
    struct QueList *next; // 下个可能密码
} StruQueList, *PtrStruQueList;

void Init(StruQueList **pQue, char *s, int cnt)
{
    (*pQue) = (PtrStruQueList)malloc(sizeof(StruQueList));
    (*pQue)->cnt = cnt;
    char *str = (char *)malloc(sizeof(char) * (strlen(s) + 1));
    if (str == NULL) {
        return;
    }
    memcpy(str, s, strlen(s) + 1);
    (*pQue)->s = str;
    (*pQue)->next = NULL;
}

// 大于0，则表示匹配成功，返回转动次数
// 等于0，则表示无异常
// 小于0，则表示出错
int NodeExpand(StruQueList **queList, StruQueList **ptrQueListLastNode, char *target)
{
    int i;
    char *s;
    // 转动1次, 当前节点演变出8种可能
    char *cur = (*queList)->s;
    int cnt = (*queList)->cnt;
    for (i = 0; i < 4; i++) {
        s = AddOne(cur, i);
        printf("%s\n", s);
        Init(&(*ptrQueListLastNode)->next, s, cnt + 1);
        if (strcmp(s, target) == 0) { // 终止条件
            return cnt + 1;
        }
        *ptrQueListLastNode = (*ptrQueListLastNode)->next;

        s = MinusOne(cur, i);
        printf("%s\n", s);
        Init(&(*ptrQueListLastNode)->next, s, cnt + 1);
        if (strcmp(s, target) == 0) {
            return cnt + 1;
        }
        *ptrQueListLastNode = (*ptrQueListLastNode)->next;
    }
    printf("\n");
    return 0;
}

// 按队列和BFS的方法来表达每次只转一次，对应的所有可能
int BfsFindMinTimes(char *cur, char *target)
{
    int ret;
    // 初始化队列 0000
    PtrStruQueList queList, queListCurLevelLast, queListLastNode;
    queList = (PtrStruQueList)malloc(sizeof(StruQueList));
    if (queList == NULL) {
        return; // if malloc is failed
    }
    Init(&queList, cur, 0);
    queListLastNode = queList;
    ret = NodeExpand(&queList, &queListLastNode, target);
    if (ret > 0) { // 终止条件
        return ret;
    }
    queListCurLevelLast = queListLastNode;
    queList = queList->next;

    // 得到两个队列的指针，一个是当前指向，一个是层级对应的新开头
    // 从所有层每个节点中迭代新的可能
    while (queList != NULL) {
        // 遍历当前层所有节点
        while (queList != queListCurLevelLast) {
            int ret = NodeExpand(&queList, &queListLastNode, target);
            if (ret > 0) { // 终止条件
                return ret;
            }
            queList = queList->next;
        }
        int ret = NodeExpand(&queList, &queListLastNode, target); // 当前层最后一个节点
        if (ret > 0) { // 终止条件
            return ret;
        }
        queList = queList->next;
        queListCurLevelLast = queListLastNode;
        printf("\n\n");
    }

    return -1; // 遍历完所有无匹配
}

// BFS
int openLock(char ** deadends, int deadendsSize, char * target)
{
    char cur[5] = "0000"; // 初始值

    // special case
    if (strcmp(cur, target) == 0) {
        return 0;
    }
    int i;
    for (i = 0; i < deadendsSize; i++) {
        if (strcmp(cur, deadends[i]) == 0) {
            return -1;
        }
    }

    int ret = BfsFindMinTimes(cur, target);

    return ret;
}
```


## 篇幅限制，请转下一篇
---

 1. [C刷题：LeetCode 752. 打开转盘锁 （中等） || BFS广搜详解 （2）](https://blog.csdn.net/qq_17256689/article/details/118692458)
