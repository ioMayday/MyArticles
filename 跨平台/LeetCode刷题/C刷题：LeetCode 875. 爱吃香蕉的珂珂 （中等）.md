**Ŀ¼**
[toc]


���⣺Cˢ�⣺LeetCode 875. �����㽶������ ���еȣ�

## ����˼·
---
**������**
	
�����ÿ���㽶��������Ҳ���Բ�������Ȼ�����Լ�дһ���������ֵ�ĺ�������һ��forѭ����ʱ�临�Ӷ�`O(N)`���ȿ��ŵ�ʱ�临�Ӷ�Ϊ`O(NlogN)`����ҪС��������**����������**������ĺô���ֱ�����ҳ���ֵ�������Լ�д��������һ��Ҳ���Լ�˼·��**���������Ĭ���Ѱ���������**

��Ŀ�У�Ŀ�����������ھ������֮ǰ�����㽶����С�ٶȣ�����Ŀ���㽶�Ķ���`N`С�ڵ��ھ��������ʱ��`H`�ģ�Ҳ������ֻҪ������������㽶���ٶ����ԣ��϶����ھ������ǰ�����ʱ���ꡣ���������ҵ�������ٶȣ�Ҳ���Ͻ硣

���ԣ�������һ���н�ģ����Ѿ�������ķ�Χ��Ϊ1������㽶����Ҳ����Ӧ������`[1, piles[numsSize - 1]]`��

ע�⣬�������и�С���ǣ����ܰ���������`piles[0]`��Ϊ��ʼ�㣬��Ϊ���羯�������ʱ��`H`�㹻������ȫ���԰��ձ�`piles[0]`��С�ĳ��㽶�ٶȳԣ�Ҳ�ܳ��ꡣ


**˼·��**

 - ��`[1, piles[numsSize - 1]]`��Χ�У������ٶ�`k` 
 - �ж��ٶ�`k`�Ƿ�����`H`Сʱ�ڳ���


## �������汾
---
ver1ʱ�临�Ӷ�Ϊ`O(N^2)`�� ������ֱ�ӱ������ύ��ʾ��ʱ��������ͨ������Ϊ`111/118`��
```c
int Compar(void *a, void *b)
{
    return *(int *)a - *(int *)b;
}

int IsOk(int* piles, int pilesSize, int h, int k)
{
    int i;
    int cnt = 0;
    for (i = 0; i < pilesSize; i++) {
        cnt += piles[i] / k;
        if (piles[i] % k != 0) { // if remains
            cnt++;
        }

        if (cnt > h) {
            return 0; // fail
        }
    }   
    return 1;
}

int minEatingSpeed(int* piles, int pilesSize, int h)
{
    if (piles == NULL || pilesSize <= 0) {
        return -1;
    }
    qsort(piles, pilesSize, sizeof(int), Compar);
    int maxVal = piles[pilesSize - 1];

    int i;
    for (i = 1; i < maxVal; i++) {
        if (IsOk(piles, pilesSize, h, i)) {
            return i;
        }
    }
    return maxVal; // ֱ�Ӱ������Ŀ�Ķ����Կ϶��ܸ���
}
```

## ���ַ��Ż��汾
----
�����������ݹ�ģ��`10^9`���ң�����`O(N^2)`�ķ����ᳬʱ����Ҫ�ö��ַ��Ż���

����`[1, piles[numsSize - 1]]`�÷�Χ�и��ص㣬ĳ��ֵ�ұ��ٶ��㹻��ȫ�����㣬ĳ��ֵ����ٶ����������㣬�е����ԣ�������ַ��Ŀ��н����ռ�Ĺ�ϵ����ת���ɶ��ַ�������߽�ⷨ��

**��������**��ʱ�临�Ӷ�`O(NlogN)`��
```c
int Compar(void *a, void *b)
{
    return *(int *)a - *(int *)b;
}

int IsNotOk(int* piles, int pilesSize, int h, int k)
{
    int i;
    int cnt = 0;
    for (i = 0; i < pilesSize; i++) {
        cnt += piles[i] / k;
        if (piles[i] % k != 0) { // if remains
            cnt++;
        }

        if (cnt > h) {
            return 1; // fail, not ok
        }
    }   
    return 0; // ok
}

int minEatingSpeed(int* piles, int pilesSize, int h)
{
    if (piles == NULL || pilesSize <= 0) {
        return -1;
    }
    qsort(piles, pilesSize, sizeof(int), Compar);
    int maxVal = piles[pilesSize - 1];

    // printf("maxVal = %d\n", maxVal);

    int left = 1;
    int right = maxVal + 1;
    int mid;
    while (left < right) {// �ҷ�����������߽�
        mid = left + (right - left) / 2;
        // printf("mid = %d\n", mid);
        if (IsNotOk(piles, pilesSize, h, mid)) { // <,����������
            left = mid + 1;
        } else { // >= ��������
            right = mid;
        }
    }
    return left;
}
```

## �ο�����
---
1. �����ܽ᣺[Cˢ�⣺���ֲ���ԭʼ�桢�������߽�/�Ҳ�߽�ģ����ܽ� | ���ַ�](https://blog.csdn.net/qq_17256689/article/details/119007921)

