**Ŀ¼**
[toc]

���⣺Cˢ�⣺LeetCode 633. ���ֲ���Ӧ��֮ƽ����֮�ͣ����ֲ��ҷ�Ӧ��

> ���ߣ���֪��
> ���ںţ���֪��
> ˢ�⽻��QQȺ��444172041

## ��Ŀ����

------


> ����˵��������һ���Ǹ����� `c` ����Ҫ�ж��Ƿ������������ `a` �� `b`��ʹ�� `a^2 + b^2 = c` ��
>
> ��Ŀ���ӣ�[633. ƽ����֮��](https://leetcode-cn.com/problems/sum-of-square-numbers/)



## ˼·����

--------



�����Ķ���������⣬�������£�

- ���Ĺ�ϵʽ��`a^2 + b^2 = c`
- Ϊ�����⣬a, b��ֱ����Ϊ�Ǹ�����ΧΪ:` [0, sqrt(c)]`
- ��������������ʾ����c�������߽�ֵ`0`��`2^15-1`

�ɵõ�����˼·�����£�

**˼·1**

- ֱ����շ�
- ����ѭ��+�����Ż�
- �����ֱ��С����`0 -> sqrt(c)`
- ʱ�临�Ӷȣ�`O(NlogN)`

**˼·2**

- ����sqrt����
- ���a��0 �� `sqrt(c)`
- �ж�`(c - a^2)`������
- ȡ���Ľ��ǰ������ֵ����Ƿ����c
- ʱ�临�Ӷȣ�`O(N)`

## ����ʵ��

--------



����˼·һ����ʵ�����Ż���`O(N^2)`����汾���£�

```c
// ԭʼ�汾��ʱ�临�Ӷ�O(N^2)���ύ��ʾ��ʱ����
bool judgeSquareSum(int c){
    float cf = sqrt(c);
    // printf("%f\n", cf);
    long long cfInt = (long long)cf;
    // printf("%ld\n", cfInt);
    long long target = (long long)c;
    long long a, b;
    for (a = 0; a <= cfInt; a++) {
        for (b = 0; b <= cfInt; b++) {
            if (a * a + b * b == target) {
                // printf("a=%lld, b=%lld\n", a, b);
                return 1;
            }
        }
    }
    return 0;
}

```



˼·һ�����ö��ַ�ʵ���Ż����`O(NlogN)`����汾���£�

```c
// �����Ż�, ʱ�临�Ӷ�O(NlogN)
bool judgeSquareSum(int c){
    float cf = sqrt(c);
    long long cfInt = (long long)cf;
    long long target = (long long)c;
    long long a;
    for (a = 0; a <= cfInt; a++) {
        long long left, right;
        left = 0;
        right = cfInt + 1;
        long long a2 = a * a;
        while (left < right) {
            long long mid = left + (right - left) / 2;
            if (a2 + mid * mid == target) {
                return 1;
            } else if (a2 + mid * mid < target) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
    }
    return 0;
}

```



˼·��������+ǰ����֤��`O(N)`����汾���£�

```c
// �㷨�Ż�, ʱ�临�Ӷ�O(N)��������sqrt����
#define DOUBLE_MIN (1e-15)
bool judgeSquareSum(int c){
    float cf = sqrt(c);
    long long cfInt = (long long)cf;
    long long target = (long long)c;
    // long long a;
    // for (a = 0; a <= cfInt; a++) {
    //     long long a2 = a * a;
    //     float bTmp = sqrt(c - a2);
    //     long long bTmpInt = (long long)bTmp;
    //     if (bTmpInt * bTmpInt + a2 == target || // ���������������⣬����99999999
    //         (bTmpInt + 1) * (bTmpInt + 1) + a2 == target ||
    //         (bTmpInt - 1) * (bTmpInt - 1) + a2 == target) {
    //         printf("a=%lld, b=%lld\n", a, (long long)(sqrt(c - a2)));
    //         return 1;
    //     }
    // }

    long long a;
    for (a = 0; a <= cfInt; a++) {
        long long a2 = a * a;
        float bTmp = sqrt(c - a2);
        long long bTmpInt = (long long)bTmp;
        // printf("%f\n", (double)bTmpInt * bTmpInt);
        double it1 = (double)a2 - (double)target;
        double sub1 = fabs((double)bTmpInt * bTmpInt + it1);
        double sub2 = fabs(((double)bTmpInt + 1) * ((double)bTmpInt + 1) + it1);
        double sub3 = fabs(((double)bTmpInt - 1) * ((double)bTmpInt - 1) + it1);
    
        if (sub1 < DOUBLE_MIN ||
            sub2 < DOUBLE_MIN ||
            sub3 < DOUBLE_MIN) {
            printf("a=%lld, b=%lld\n", a, (long long)(sqrt(c - a2)));
            return 1;
        }
    }
    
    return 0;
}
```



**��������룬���뻹�����Ż��𣬻��ܸ����ǿЩ��**

�Լ��о�����󣬿����¹ٷ���𣬷��ֻ��мٶ�`a <= b`���˫ָ���˼·��ʵ��`O(N)`������һ�����÷���ƽ���Ͷ������ѧ�������ж��Ƿ���ڣ������޷�����`O(N)`��ʱ�临�Ӷȡ�

## �ο�����

------



1. [LeetCode�ٷ���⣺ƽ����֮��](https://leetcode-cn.com/problems/sum-of-square-numbers/solution/ping-fang-shu-zhi-he-by-leetcode-solutio-8ydl/)

