**Ŀ¼**
[toc]

���⣺Cˢ�⣺LeetCode 752. ��ת���� ���еȣ�



> PS��
> ����Ϊ���е��⣬����ٷ��������`A*`�㷨��û���ֳɵ����ݽṹ����C����ʵ�֣����������⣬�����ֲ������ٷ���`A*`�㷨ʵ���ﶼ����дC�汾�ˡ�����
> ���ԣ��ҾͲ�����ô������������BFSһ�������룬���ѷ�����˼·��¼�ڴ˰ɡ�

## ˼·����
---
 - ��ʵ����򵥵ģ�����������п���
 - �Ӻ�����ǰ����ת���Ĺ���
 - ����ʵ��BFS�����ҵ�target 
 - �ټ�Լ��deadends���� + ��С���� 


## ԭʼ�汾
---
����ʵ��һ���������ܣ�˼·�ǰ�һ��λת����ͬ������Ȼ��ڶ���λת��ͬ����������ݹ飬������DFS�������������£�
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
    // ��ֹ����
    if (depth == 4) {
        return;
    }

    // ��������
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
    char cur[5] = "0000"; // ��ʼֵ
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


## �汾һ��BFS������п���
---
����ԭʼ�汾˼·���������������ο�labuladong��˼·��������BFS���֣�**������п���**���������£�

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
    int cnt; // ת������
    char *s; // ��ǰ����
    struct QueList *next; // �¸���������
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
    // ��ʱת��1��, ��ǰ�ڵ��ݱ��8�ֿ���
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

// �����к�BFS�ķ��������ÿ��ֻתһ�Σ���Ӧ�����п���
void BfsFindMinTimes(char *cur, char *target)
{
    // ��ʼ������ 0000
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

    // �õ��������е�ָ�룬һ���ǵ�ǰָ��һ���ǲ㼶��Ӧ���¿�ͷ
    // �����в�ÿ���ڵ��е����µĿ���
    while (queList != NULL) {
        queListCurLevelLast = queListLastNode;
        // ������ǰ�����нڵ�
        while (queList != queListCurLevelLast) {
            queListLastNode = NodeExpand(&queList, queListLastNode);
            queList = queList->next;
        }
        queListLastNode = NodeExpand(&queList, queListLastNode); // ��ǰ�����һ���ڵ�
        queList = queList->next;
        printf("\n\n");
    }

    return;
}

// BFS
int openLock(char ** deadends, int deadendsSize, char * target)
{
    char cur[5] = "0000"; // ��ʼֵ

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
## �汾�����ҵ�target����ֹ
---
���ż����Ľ���������ֹ������ʵ���ҵ�target��ֹͣ������
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
    int cnt; // ת������
    char *s; // ��ǰ����
    struct QueList *next; // �¸���������
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

// ����0�����ʾƥ��ɹ�������ת������
// ����0�����ʾ���쳣
// С��0�����ʾ����
int NodeExpand(StruQueList **queList, StruQueList **ptrQueListLastNode, char *target)
{
    int i;
    char *s;
    // ת��1��, ��ǰ�ڵ��ݱ��8�ֿ���
    char *cur = (*queList)->s;
    int cnt = (*queList)->cnt;
    for (i = 0; i < 4; i++) {
        s = AddOne(cur, i);
        printf("%s\n", s);
        Init(&(*ptrQueListLastNode)->next, s, cnt + 1);
        if (strcmp(s, target) == 0) { // ��ֹ����
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

// �����к�BFS�ķ��������ÿ��ֻתһ�Σ���Ӧ�����п���
int BfsFindMinTimes(char *cur, char *target)
{
    int ret;
    // ��ʼ������ 0000
    PtrStruQueList queList, queListCurLevelLast, queListLastNode;
    queList = (PtrStruQueList)malloc(sizeof(StruQueList));
    if (queList == NULL) {
        return; // if malloc is failed
    }
    Init(&queList, cur, 0);
    queListLastNode = queList;
    ret = NodeExpand(&queList, &queListLastNode, target);
    if (ret > 0) { // ��ֹ����
        return ret;
    }
    queListCurLevelLast = queListLastNode;
    queList = queList->next;

    // �õ��������е�ָ�룬һ���ǵ�ǰָ��һ���ǲ㼶��Ӧ���¿�ͷ
    // �����в�ÿ���ڵ��е����µĿ���
    while (queList != NULL) {
        // ������ǰ�����нڵ�
        while (queList != queListCurLevelLast) {
            int ret = NodeExpand(&queList, &queListLastNode, target);
            if (ret > 0) { // ��ֹ����
                return ret;
            }
            queList = queList->next;
        }
        int ret = NodeExpand(&queList, &queListLastNode, target); // ��ǰ�����һ���ڵ�
        if (ret > 0) { // ��ֹ����
            return ret;
        }
        queList = queList->next;
        queListCurLevelLast = queListLastNode;
        printf("\n\n");
    }

    return -1; // ������������ƥ��
}

// BFS
int openLock(char ** deadends, int deadendsSize, char * target)
{
    char cur[5] = "0000"; // ��ʼֵ

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


## ƪ�����ƣ���ת��һƪ
---

 1. [Cˢ�⣺LeetCode 752. ��ת���� ���еȣ� || BFS������� ��2��](https://blog.csdn.net/qq_17256689/article/details/118692458)
