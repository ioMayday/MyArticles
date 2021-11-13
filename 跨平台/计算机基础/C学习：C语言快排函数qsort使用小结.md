**Ŀ¼**
[toc]

> ���ߣ���֪��
> ���ںţ���֪��
> ��飺������˶ʿ����Ϊ�㷨����ʦ��רע������顢ְ������������������������

���⣺Cѧϰ��C���Կ��ź���qsortʹ��С��

> ������ͷ�ļ� `<stdlib.h>`
> ��Ҫ��ʾ��qsort��������**���ȶ���**�����ܻᵼ����ͬ��С��Ԫ�ط���λ�ý�������ע��Ӧ�ó�����

## ����ԭ��
----

```c
void qsort(void *base, size_t nmemb, size_t size, int(*compar)(const void *, const void *))
```
**����˵����**
- base: Ҫ��������飬�� `int*` ���͵�ָ����������׵�ַ
- nmemb:����Ԫ�ظ������� `int a[12]` ��ʾ12��int����Ԫ��
- size:ÿ��Ԫ����ռ�ڴ�ռ䣬��λΪ�ֽ�byte������ `int` ����ͨ��Ϊ`2 byte`������`sizeof(int)/sizeof(int*)`��ȡ
- compar:�Ƚ�����Ԫ�صıȽϺ���
- �����Զ�ʵ��`int/float/double`�Ȼ����������͵�����
- ������`stdlib.h`ͷ�ļ���

**����������compar**
�ú���ͨ�����Զ���ģ�������ȡ����ʽ���£�
```c
int compar(const void *a,const void *b)
```
- �������͸�������Զ��壬**����ֵ����0ʱ��a��b����˳��**
- a��b���߼��ϱȽϣ�**�����������֣��ַ�Ҳ��**
- a��bͨ����ָ��ͬԪ�صĵ�ַָ�룬����ʱ��Ҫ�� `*a` ������ 

```c
- �� a > b������ >0
- �� a == b������ 0
- �� a < b������ <0
```


## С������
----
**��һ�������ֵ**

```c
#define NUMSIZE 5
int a[NUMSIZE] = {5, 2, 1, 3, 4};
int cmp(const void* a, const void *b) {
    // *(int*)a��ʾ��aǿת��int*���ͺ��ٽ����ã��õ���ָ���ֵ
	return *(int*)a - *(int*)b; // ���� 
	// return *(int*)b - *(int*)a; // ����
}
qsort(a, NUMSIZE, sizeof(int), cmp); //ִ�к�����a����������
```

**��������ά�����ֵ����**

```c
int a[1000][50];  // ���а���a[i][2]����2�е����ݴ�С��Ϊ�������ݣ�����a[i][0]�����a[i][49]һ�����������ƶ�������
int comp(const void *a,const void *b)
{
     return ((int *)a)[2]-((int *)b)[2]; // �������ݣ��Ƚ�ĳ����Ԫ��
}
// ��aΪ��ʼ��ַָ�룬����compare���������ʼλ������sizeof(int)*2��һ���鿽��������Ҳ�����н���
qsort(a,1000,sizeof(int)*50,comp);
```

**�������ṹ���ֵ**

```c
// @example.c
#include <stdlib.h>  //for qsort function
struct ObjNums {
    int val;
    int index;
};
static int cmp(const void* a, const void* b)  //�Ƚ����
{
    return ( *(struct ObjNums *)a ).val - ( *(struct ObjNums *) b).val; //��С�������򣬽���return��a��b��Ӵ�С����
}
void main() {
	struct ObjNums *sortedNums = (struct ObjNums *) malloc(numsSize * sizeof(*sortedNums));
	... //��sortedNums��ʼ����ֵ
	qsort(sortedNums, numsSize, sizeof(*sortedNums), cmp); // ����,��С����
}
```			

## Ӧ��ʵ��
----
��Leetcode��`twoSum`���Ӧ��Ϊ����

```c
struct ObjNums {
    int val;
    int index;
};

static int cmp(const void* a, const void* b)  //�Ƚ����
{
    return ( *(struct ObjNums *)a ).val - ( *(struct ObjNums *) b).val; //��С�������򣬽���return��a��b��Ӵ�С����
}

int* twoSum(int* nums, int numsSize, int target, int* returnSize)
{
    int *ret = (int *) malloc( 2 * sizeof(int));
    struct ObjNums *sortedNums = (struct ObjNums *) malloc(numsSize * sizeof(*sortedNums));
    int i,j;
    *returnSize = 0; 

    for(i=0; i < numsSize; i++) { // ��������
        sortedNums[i].val = nums[i];
        sortedNums[i].index = i;
    }
    qsort(sortedNums, numsSize, sizeof(*sortedNums), cmp); // ����,��С����

    i = 0;
    j = numsSize - 1;
    while (i < j) { // �б����
        if (sortedNums[i].val + sortedNums[j].val < target) {
            i++; // �����������ߺ�
        }
        else if (sortedNums[i].val + sortedNums[j].val > target) {
            j--; // ���Ƽ�С���ߺ�
        }
        else {
            ret[0] = sortedNums[i].index;
            ret[1] = sortedNums[j].index;
            *returnSize = 2; // ���Ҫ�з���ֵ��С����Ȼret���鲻֪��������ٸ�
            free(sortedNums);
            return ret;
        }
    }
    free(sortedNums);
    return ret; 
}
```

**ע������**
 - qsort������һ������Ϊ�����׵�ַ
 - �Ը��������ݱȽ�ʱ���������������ɣ�����0������С��0����
 - ע��cmp�������ڲ�ʵ�֣���֤Ϊ��ӦԪ������
 

## �ο�����
---
1. [��Ҫ�� C���ԵĿ��ź���qsort()](https://blog.csdn.net/qq_41799219/article/details/80626467)
2. [C���Կ��ź���qsort()](https://blog.csdn.net/f_zyj/article/details/51484751)
3. [C���Ա�׼�⺯��qsort����Ľ�����ʹ��](https://blog.csdn.net/masibuaa/article/details/5633498)



