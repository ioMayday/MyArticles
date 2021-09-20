# Cˢ�⣺LeetCode 165.�Ƚϰ汾��
[toc]

## ǰ��
---

**Git��Ŀ��ַ**��[LeetCodeUsingCˢ��ʼ�](https://github.com/ioMayday/leetcode_sol)

������ΪC���Թ��ڵײ㣬��෽�����ݽṹ�Ϳ⺯����û�У���������**��C����ˢLeetCode����Ŀ������**��

Ϊ���������������������ǻ��Ǵӵײ�������Ϥ��ɣ��Լ���һ�����ԣ��Ժ���ܸ��ݽ�겣���������������ʵ��ʱ������Ҳ�ܸ�����Ӧ�֡�

��**ϵ�в���**�����ˢ��ÿ��LeetCode�ⶼ�ó���������**����Կ��ǽϾá���Щdebug���ɵ���Ŀ**������������ô������Щ�ӡ�������Ŀ�ⷨ�ο�Git��Ŀ��[LeetCodeUsingCˢ��ʼ�](https://github.com/ioMayday/leetcode_sol)
��

## ֪ʶ��
---
 - �ַ��ָ��`strtok()`�÷�
 - �ַ�ת����`atoi()`�÷�
 - ���������Ĵ���

## ��Ŀ����
----
��Ŀ���ӣ�[LeetCode 165.�Ƚϰ汾��](https://leetcode-cn.com/problems/compare-version-numbers/)����Ŀ��**����**Ϊ�����ַ�����һ����`�汾��1`��һ����`�汾��2`�����ж������汾�ŵĸߵͣ�**���**��`0/1/-1`��Ϊ��ͬ���ߡ��͵ı�־��

**�ַ����ص㣺**
 - �� `.` ��Ϊ�ָ��
 - ǰ��`0`��Ч
 - ��������������ǰ��汾����ͬʱ��`.` ���`ȫ��0`����Ч���з�0���򳤶���Ч

## ��Ҫ˼·
---

 - �Ȱ������ַ����ĸ��Էָ�תΪ���飬����¼���鳤��
 - ������С���ȵ����飬�Ƚ���ֵ��һ�����ֲ��ȣ�����Է���`1/-1`
 - �Խ�С���ȵ��������ֵ�����ж�ȫ��ȣ����жϣ�
   - ��Ϊ�ַ����ص��3���ȫ0��Ч������򷵻�0
   - ��������ĳ�����Ч�����ȴ�İ汾��

## ���뿪��
---
> ִ����ʱ�� **0 ms** , ������ C �ύ�л����� 100.00% ���û� <br>
> �ڴ����ģ� **5.5 MB** , ������ C �ύ�л�����  54.51% ���û�

## ʵ�ִ���
---
C����汾���£�

```c
int SplitStr(char *version, const char *split, int *verNum, int len)
{
    char *p = strtok(version, split);
    while (p) {
        verNum[len++] = atoi(p);
        p = strtok(NULL, split);
    }
    return len;
}

int compareVersion(char * version1, char * version2)
{
    // �и�ver1��ver2���ŵ����飬����¼����
    int ver1[500] = {0};
    int ver2[500] = {0};
    int lenVer1 = 0, lenVer2 = 0;
    const *split = ".";
    lenVer1 = SplitStr(version1, split, ver1, lenVer1);
    lenVer2 = SplitStr(version2, split, ver2, lenVer2);
    
    // �Ƚ�����
    int minLen = lenVer1 < lenVer2 ? lenVer1 : lenVer2;
    int i;
    for (i = 0; i < minLen; i++) {
        if (ver1[i] > ver2[i]) {
            return 1;
        } else if (ver1[i] < ver2[i]) {
            return -1;
        }
    }
    // �����������
    // "1.0"
    // "1.0.0"
    // �жϳ��汾�ţ������Ƿ�Ϊ0
    if (minLen == lenVer1) { // ������lenVer2
        for (i = lenVer1; i < lenVer2; i++) {
            if (ver2[i] != 0) {
                break;
            }
        }
        if (i == lenVer2) { // ���汾�ź���ȫΪ0
            return 0;
        }
    } else {
        for (i = lenVer2; i < lenVer1; i++) {
            if (ver1[i] != 0) {
                break;
            }
        }
        if (i == lenVer1) { // ���汾�ź���ȫΪ0
            return 0;
        }
    }

    // ��С���������˳���ǰ��break
    // ��ÿ���Ŷ���ͬ�ҳ��汾�ź�����ȫΪ0
    // �Ƚ϶���
    if (lenVer1 > lenVer2) {
        return 1;
    } else if (lenVer1 < lenVer2) {
        return -1;
    }
    // lenVer1 == lenVer2)
    return 0;  // �������
}
```
