
**Ŀ¼**
[toc]
���⣺Cˢ�⣺LeetCode 752. ��ת���� ���еȣ� ��ƪ


����һƪ���ͣ�[Cˢ�⣺LeetCode 752. ��ת���� ���еȣ� || BFS������� ��1��](https://blog.csdn.net/qq_17256689/article/details/118661633)


## Hash�汾
---

**���HASH���ݽṹ**����Ϥuthash��ʹ�ã����Լ��������

**��ҪԼ������**��
- ���߻�ͷ·��������ǰת����һ�εĽ����������������ת����ȥ
- ����Խ��deadends
- ������ֹ������һ��ƥ�䵽target�ͷ���

**���߻�ͷ·**��������ǰת����һ�εĽ����������������ת����ȥ�����ʼ״̬`0000`��ת��һ�α�Ϊ`1000`����ת��һ�β���ת��ȥ��Ϊ`0000`��ʵ�ֵ���Ҫ˼·�ǣ�����һ��hash��`visit`��¼�����Ѿ��еı�����������������ͬ�����������Ҳ���¼����Ҫ��uthash��ϣ���O(1)���ҹ��ܣ����ٲ���ʱ�䡣

**����Խ��deadends**��Ҳ������visit��hash�����˼·ʵ�֡�hash��ʱ�临�Ӷ�ΪO(1)���������Կռ任ʱ�䣬����ʱ���ͬʱ�������ռ䡣

**��ֹ����**�����ڸ�˼·������Ǵ�ת������0��ʼ������չ�ģ�����һ��̰���㷨˼�룬ÿ���������ŵ��ˡ�һ��ƥ����Ŀ�꣬��ǰת������������С�ģ����Կ���ֱ�ӷ��ء�


�ύͨ����hash�汾�������£�

```c
#define STR_SIZE 5
#define STR_LEN 4
int g_curLevelCnt;

typedef struct HashTable {
    char str[STR_SIZE]; // key
    UT_hash_handle hh; // table head
} StruHashTable;

typedef struct QueList {
    int cnt; // ת������
    char *s; // ��ǰ����
    struct QueList *next; // �¸���������
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

// ����0�����ʾƥ��ɹ�������ת������
// ����0�����ʾ���쳣
// С��0�����ʾ����
int NodeExpand(StruQueList **queList, StruQueList **ptrQueListLastNode, char *target, StruHashTable **ptrDead, StruHashTable **ptrVisit)
{
    int i;
    char *s;
    StruHashTable *hashTmp1, *hashTmp2;

    // ת��1��, ��ǰ�ڵ��ݱ��8�ֿ���
    char *cur = (*queList)->s;
    int cnt = (*queList)->cnt;
    for (i = 0; i < 4; i++) {
        s = AddOne(cur, i);
        // �����targetƥ��
        if (strcmp(s, target) == 0) { // ��ֹ����
            return cnt + 1;
        }

        // �����deadends
        HASH_FIND(hh, *ptrDead, s, sizeof(char) * STR_SIZE, hashTmp1);
        // ����ѱ���
        HASH_FIND(hh, *ptrVisit, s, sizeof(char) * STR_SIZE, hashTmp2);
        if (hashTmp1 == NULL && hashTmp2 == NULL) { // ����dead��Ҳû��visit��
            Init(&(*ptrQueListLastNode)->next, s, cnt + 1);
            *ptrQueListLastNode = (*ptrQueListLastNode)->next;
            hashTmp1 = (StruHashTable *)malloc(sizeof(StruHashTable)); // ����һ��hash�ڵ�
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
        // �����targetƥ��
        if (strcmp(s, target) == 0) { // ��ֹ����
            return cnt + 1;
        }
        // �����deadends
        HASH_FIND(hh, *ptrDead, s, sizeof(char) * STR_SIZE, hashTmp1);
        // ����ѱ���
        HASH_FIND(hh, *ptrVisit, s, sizeof(char) * STR_SIZE, hashTmp2);
        if (hashTmp1 == NULL && hashTmp2 == NULL) { // ����dead��Ҳû��visit��
            Init(&(*ptrQueListLastNode)->next, s, cnt + 1);
            *ptrQueListLastNode = (*ptrQueListLastNode)->next;
            hashTmp1 = (StruHashTable *)malloc(sizeof(StruHashTable)); // ����һ��hash�ڵ�
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

// �����к�BFS�ķ��������ÿ��ֻתһ�Σ���Ӧ�����п���
int openLock(char ** deadends, int deadendsSize, char * target)
{
    char cur[STR_SIZE] = "0000"; // ��ʼֵ
    int i, ret;

    // special case
    if (strcmp(cur, target) == 0) {
        return 0;
    }

    // ��ʼ��hash
    StruHashTable *dead = NULL; // ��ͷ�ʼ��Ϊ��
    StruHashTable *hashTmp;
    for (i = 0; i < deadendsSize; i++) {
        HASH_FIND(hh, dead, deadends[i], sizeof(char) * STR_SIZE, hashTmp); // ��ֵ��ռ�ռ�sizeof(char) * 5
        if (hashTmp == NULL) { // ֮ǰδ����
            hashTmp = (StruHashTable *)malloc(sizeof(StruHashTable)); // ����һ��hash�ڵ�
            if (hashTmp == NULL) {
                return -1;
            }
            memcpy(hashTmp->str, deadends[i], STR_SIZE);
            HASH_ADD(hh, dead, str, sizeof(char) * STR_SIZE, hashTmp); // str��ʾ�����ṹ���еļ�ֵ��׷�ӵ�hashtable��
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

    // ��ʼ������ 0000
    StruHashTable *visit = NULL; // ��ͷ�ʼ��Ϊ��
    PtrStruQueList queList, queListCurLevelLast, queListLastNode;
    queList = (PtrStruQueList)malloc(sizeof(StruQueList));
    if (queList == NULL) {
        return -1; // if malloc is failed
    }
    g_curLevelCnt = 0;
    Init(&queList, cur, 0);
    hashTmp = (StruHashTable *)malloc(sizeof(StruHashTable)); // ����һ��hash�ڵ�
    if (hashTmp == NULL) {
        return -1;
    }
    memcpy(hashTmp->str, queList->s, STR_SIZE);
    // printf("%p\n", visit);
    HASH_ADD(hh, visit, str, sizeof(char) * STR_SIZE, hashTmp); // ����һ���ѱ���״̬
    // printf("%p\n", visit);

    g_curLevelCnt = 0;
    queListLastNode = queList;
    ret = NodeExpand(&queList, &queListLastNode, target, &dead, &visit);
    if (ret > 0) { // ��ֹ����
        return ret;
    }
    queListCurLevelLast = queListLastNode;
    queList = queList->next;

    // �����в�ÿ���ڵ��е����µĿ���
    while (queList != NULL) {
        // ������ǰ�����нڵ�
        int len = g_curLevelCnt;
        g_curLevelCnt = 0;
        for (i = 0; i < len; i++) {
            ret = NodeExpand(&queList, &queListLastNode, target, &dead, &visit);
            if (ret > 0) { // ��ֹ����
                return ret;
            }
            queList = queList->next;
        }
    }

    return -1; // ������������ƥ��
}
```

## Hash�汾�Ż�
---
**���뾫��**��ɾ��������Ϣ�������ظ�����Ϊ�����Ӻ�����

 - ɾ���ϲ��ظ�������ٲ���������� 
 - ����50�еĴ����ֳ��ӹ��ܺ���
 - �滻ħ������Ϊ������ĺ�����
 - �Ż�whileѭ����lastNode�ж�Ϊforѭ��������ȫ�ֱ���������ǰ�������Ϊlen

��Ҫ�仯�Ĵ������£�
```c
void InitDeadHash(char **deadends, int deadendsSize, StruHashTable **ptrDead)
{
    int i;
    StruHashTable *hashTmp;
    for (i = 0; i < deadendsSize; i++) {
        HASH_FIND(hh, *ptrDead, deadends[i], sizeof(char) * STR_SIZE, hashTmp); // ��ֵ��ռ�ռ�sizeof(char) * 5
        if (hashTmp == NULL) { // ֮ǰδ����
            hashTmp = (StruHashTable *)malloc(sizeof(StruHashTable)); // ����һ��hash�ڵ�
            if (hashTmp == NULL) {
                return;
            }
            memcpy(hashTmp->str, deadends[i], STR_SIZE);
            HASH_ADD(hh, *ptrDead, str, sizeof(char) * STR_SIZE, hashTmp); // str��ʾ�����ṹ���еļ�ֵ��׷�ӵ�hashtable��
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
    hashTmp = (StruHashTable *)malloc(sizeof(StruHashTable)); // ����һ��hash�ڵ�
    if (hashTmp == NULL) {
        return;
    }
    memcpy(hashTmp->str, (*ptrQueList)->s, STR_SIZE);
    HASH_ADD(hh, *ptrVisit, str, sizeof(char) * STR_SIZE, hashTmp); // ����һ���ѱ���״̬
    return;
}

int DealCurStr(char *s, char *target, int cnt, StruQueList **ptrQueListLastNode, StruHashTable **ptrDead, StruHashTable **ptrVisit)
{
    StruHashTable *hashTmp1, *hashTmp2;
    // �����targetƥ��
    if (strcmp(s, target) == 0) { // ��ֹ����
        return cnt + 1;
    }

    // �����deadends
    HASH_FIND(hh, *ptrDead, s, sizeof(char) * STR_SIZE, hashTmp1);
    // ����ѱ���
    HASH_FIND(hh, *ptrVisit, s, sizeof(char) * STR_SIZE, hashTmp2);
    if (hashTmp1 == NULL && hashTmp2 == NULL) { // ����dead��Ҳû��visit��
        Init(&(*ptrQueListLastNode)->next, s, cnt + 1);
        *ptrQueListLastNode = (*ptrQueListLastNode)->next;
        hashTmp1 = (StruHashTable *)malloc(sizeof(StruHashTable)); // ����һ��hash�ڵ�
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

// ����0�����ʾƥ��ɹ�������ת������
// ����0�����ʾ���쳣
// С��0�����ʾ����
int NodeExpand(StruQueList *queList, StruQueList **ptrQueListLastNode, char *target, StruHashTable **ptrDead, StruHashTable **ptrVisit)
{
    int i, ret;
    char *s;

    // ת��1��, ��ǰ�ڵ��ݱ��8�ֿ���
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
    // �õ��������е�ָ�룬һ���ǵ�ǰָ��һ���ǲ㼶��Ӧ���¿�ͷ
    // �����в�ÿ���ڵ��е����µĿ���
    int i, ret;
    while (queList != NULL) {
        // ������ǰ�����нڵ�
        int len = g_curLevelCnt;
        g_curLevelCnt = 0;
        for (i = 0; i < len; i++) {
            ret = NodeExpand(queList, ptrQueListLastNode, target, ptrDead, ptrVisit);
            if (ret > 0) { // ��ֹ����
                return ret;
            }
            queList = queList->next;
        }
        // printf("\n\n");
    }

    return 0;
}

// �����к�BFS�ķ��������ÿ��ֻתһ�Σ���Ӧ�����п���
int openLock(char ** deadends, int deadendsSize, char * target)
{
    char cur[STR_SIZE] = "0000"; // ��ʼֵ
    int ret;

    // special case
    if (strcmp(cur, target) == 0) {
        return 0;
    }

    // ��ʼ��dead hash
    StruHashTable *dead = NULL; // ��ͷ�ʼ��Ϊ��
    StruHashTable *hashTmp, *hashTmp1, *hashTmp2;
    InitDeadHash(deadends, deadendsSize, &dead);

    // special case
    HASH_FIND(hh, dead, target, sizeof(char) * STR_SIZE, hashTmp1);
    HASH_FIND(hh, dead, cur, sizeof(char) * STR_SIZE, hashTmp2);
    if (hashTmp1 != NULL || hashTmp2 != NULL) {
        return -1;  // deanends contain target
    }

    // ��ʼ������0000��visit hash
    StruHashTable *visit = NULL; // ��ͷ�ʼ��Ϊ��
    PtrStruQueList queList, queListLastNode;
    InitQueAndVisitHash(cur, &queList, &visit);

    queListLastNode = queList;
    ret = LevelTraverse(queList, &queListLastNode, target, &dead, &visit);
    if (ret > 0) { // ��ֹ����
        return ret;
    }
    return -1; // ������������ƥ��
}
```

## չ��
----

 - �ɲο��ٷ����д������openLock()��ѭ������ʵ��
 - �ɲ���˫��BFS��һ�����Ч��
 - �ɲ�������ʽ����`A*`�㷨��һ�����Ч��
