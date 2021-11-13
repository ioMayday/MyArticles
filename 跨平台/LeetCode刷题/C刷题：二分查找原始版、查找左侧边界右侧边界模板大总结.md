**Ŀ¼**
[toc]

���⣺Cˢ�⣺���ֲ���ԭʼ�桢�������߽�/�Ҳ�߽�ģ����ܽ�


## ����
---
���ַ�����������Ͳ��ҳ�����������`logN`��ʱ�临�Ӷȣ����ҵ�ĳֵ�����������䣬Ч�ʽϸߣ�ʹ��Ƶ�������־������׳����ʴ��ܽ��÷���������Ļعˡ�

�Ա߽�ֵ�Ͳ������ʱ���±��ƶ������Ҫ���͸����������ϰ���顣**��������ע�������ʵ��ϸ�ڣ�����ֱ������ĩβ�������ֶԱ��ܽ���һĿ��Ȼ��**

## ���ֲ���ֵ
---

**������������**��
- ����ҿ���`[left, right)`����������һ��Ϊ`left == right`
- ����ұգ�`[left, right]`����������һ��Ϊ`left > right`


������δ��ȷ˵���ģ�ʹ�õ�����ͳһΪ����ҿ���`[left, right)`��

**��д����汾����**��

 - ��������������ĳֵ�Ƿ����
 - �����ڣ��򷵻����±�
 - �������ڣ��򷵻�-1
 - ��ĳֵ�������д��ڶ���������±�δ��

����ʵ�����£�
```c
// ���룺�������飬�����С�������ҵ�Ŀ��
// ����������ҵ����򷵻ض�Ӧ�±ꣻ���򷵻�-1
int binarySearch(int *nums, int numsSize, int target)
{
    int left = 0;
    int right = numsSize;
    int mid;
    while (left < right) {
        mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            return mid; // ���ҵ�
        } else if (nums[mid] < target) { // �����������
            left = mid + 1;
        } else if (nums[mid] > target) { // �Ҳ���������
            right = mid;
        }
    }
    return -1; // û�ҵ����򷵻�-1
}
```

**�⺯���汾����**��

 - ��������������ĳֵ�Ƿ����
 - �����ڣ��򷵻���ֵ���ڵ�Ԫ��ָ��
 - �������ڣ��򷵻�NULL
 - ��ĳֵ�������д��ڶ�������ص�ֵ�������ĸ��ظ�ֵ��ȷ��


����ǩ����`void *bsearch(const void *_Key, const void *_Base, size_t _NumOfElements, size_t _SizeOfElements, int (*_PtFuncCompare)(const void *, const void *) __attribute__((cdecl)))`

ͷ�ļ���`#include <stdlib.h>`

��Ҫ˵����

 - �����������Ϊ������򣬵����һ��compar����������֮��Ӧ
 - �����Ŀ�����Ԫ�ر���Ϊ���Ӧָ���ַ����Ϊ��������ᱨ��
 - ����ֵΪԪ��ָ�����NULL
 - Ŀ��ֵ����λ���±��ͨ������ֵ��ַ���׵�ַ����õ���ǰ��������Ԫ���ڴ�����


**����demo��������**��
```c
#include <stdlib.h>

int Compar(void *a, void *b)
{
    return *(int *)a - *(int *)b;
}

// �����������ֵ
int main(void)
{
    int nums[10] = {0, 0, 2, 2, 4, 5, 6, 9, 9, 9};
    int numsSize = 10;
    int key = 6;
    int *ret = (int*)bsearch(&key, nums, numsSize, sizeof(int), Compar);

    if (ret != NULL) {
        printf("target == %d, index = %d\n", *ret, (int)(ret - &nums[0])); // ����±�
    } else {
        printf("target not found\n");
    }

    return 0;
}
```

## ���ֲ�����߽�
---
**�������ұ߽�ָ�������������ظ�ֵ�����Ҳ࣬Ϊ������**���� `int nums[10] = {0, 0, 2, 2, 4, 5, 6, 9, 9, 9};`��������Ŀ��ֵΪ9�ķ�Χ��`[7, 9]`�������߽��±�Ϊ7�� �Ҳ�߽��±�Ϊ9��

��һ���汾����ǿ�棬��������ظ�ֵ���ҵ�����Ŀ��ֵ���ֵ���߽硣
```c
// ���룺�������飬�����С�������ҵ�Ŀ�꣬�����ظ�ֵ
// ����������ҵ����򷵻س��ָ�ֵ�����߽��Ӧ�±ꣻ���򷵻�-1
int binarySearchLowerBound(int *nums, int numsSize, int target)
{
    int left = 0;
    int right = numsSize;
    int mid;
    int flag = 0; // Ĭ��û�ҵ�
    while (left < right) {
        mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            right = mid; // ���ҵ�����С������
            flag = 1;
        } else if (nums[mid] < target) { // �����������
            left = mid + 1;
        } else if (nums[mid] > target) { // �Ҳ���������
            right = mid;
        }
    }
    if (flag == 1) { // �ҵ�
        return left; // right = mid��left����midʱ����
    }
    return -1; // û�ҵ����򷵻�-1
}
```

## ���ֲ����ұ߽�
---
����ʵ�����£�
```c
// ���룺�������飬�����С�������ҵ�Ŀ�꣬�����ظ�ֵ
// ����������ҵ����򷵻س��ָ�ֵ���Ҳ�߽��Ӧ�±ꣻ���򷵻�-1
int binarySearchUpperBound(int *nums, int numsSize, int target)
{
    int left = 0;
    int right = numsSize;
    int mid;
    int flag = 0; // Ĭ��û�ҵ�
    while (left < right) {
        mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            left = mid + 1; // ���ҵ�����С������
            flag = 1;
        } else if (nums[mid] < target) { // �����������
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid; // ע��
        }
    }
    if (flag == 1) { // �ҵ�
        return left - 1; // ��mid���ʱ��left = mid + 1
    }
    return -1; // û�ҵ����򷵻�-1
}
```


## ���ұ߽���Ҷ���һ��
---
�������ұ߽�Ŀ��ƣ���bound��ξ�������`bound = 0`��������߽磻��`bound = 1`�������ұ߽硣

**����ֵ����**������߽磬���ʱ������С�ұ߽磬�������Ȳ����������Է���`left`�����ұ߽磬���ʱ������С��߽磬���Ҳ���Ȳ��������Ҳ����ʱ��`right`�ǿ�����ȡ�����ģ�������`right - 1`��

������Է��֣��ú����������ģ�����ʵ�ֲ������������ظ����ֵ�ĳֵ���䷶Χ�Ĺ��ܣ��������ΧΪ����ҿ���ֻ��Ҫ��`bound = 1`ʱ������ֵ`right - 1`����Ϊ`right`���ɣ�Ҳ�����ص�һ������Ŀ��ֵ���±ꡣ

**����ʵ��**���£�

```c
// ���룺�������飬�����С�������ҵ�Ŀ�꣬�����ظ�ֵ
// ����������ҵ����򷵻س��ָ�ֵ���Ҳ�߽��Ӧ�±ꣻ���򷵻�-1
int binarySearchBound(int *nums, int numsSize, int target, int bound)
{
    int left = 0;
    int right = numsSize;

    while (left < right) {
        int mid = left + (right - left) / 2;
        int condition = (bound == 0) ? (nums[mid] < target) : (nums[mid] <= target); // �ϲ�==
        if (condition) {
            left = mid + 1; // ����������
        } else {            // �Ҳ��������
            right = mid;
        }
    }

    if (bound == 0 && nums[left] == target) { // �������߽�
        return left;
    } 
    if (bound == 1 && nums[right - 1] == target) { // �����Ҳ�߽�
        return right - 1; // = left - 1
    } 
    return -1;
}
```

## ���ֲ����ܽ�
---

��Ҫע����ǣ�������target��ʱ��ֱ�ӷ���-1���ڶ��ֲ���ֵʱ������������`nums[mid] == target`ʱֱ��return,���������Ҳ�߽�ʱ��������������Ҫ��`while()`ѭ����Ϻ󣬲��ܷ��ء��۲��±��֪��**�����Ҳ࿪����ҪӰ��right�ĸ��º�while�ж�**��

|          ����          |   ����ҿ�[left, right)    |     ����ұ�[left, right]      |   ��ע   |
| :--------------------: | :------------------------: | :----------------------------: | :------: |
|        ��ʼ��ֵ        | left = 0, right = numsSize | left = 0, right = numsSize - 1 | ���ֲ�ͬ |
|       while����        |        left < right        |         left <= right          |   ��ͬ   |
| `nums[mid] < target `  |       left = mid + 1       |         left = mid + 1         |   ��ͬ   |
| `nums[mid] > target `  |        right = mid         |        right = mid - 1         |   ��ͬ   |
| `nums[mid] == target ` |          ����mid           |            ����mid             |   ��ͬ   |



�������Ҳ�߽���Ҳ��õ�������ҿ����䣬��������Ȥ�����з�������ұ������Ӧ�������**ע��**���������߽粻���ڵĳ�������whileѭ����Ҫ�ж��±��Ӧֵ�Ƿ���target��ȡ�

�۲��±��֪�������俪�������ͬʱ��**���Ҳ�߽�Ĳ��ҵ���Ҫ��������`nums[mid] == target`ʱ�߽���ºͷ���ֵ**��

|         ����          |          ���߽�          |          �Ҳ�߽�          | ��ע |
| :-------------------: | :------------------------: | :------------------------: | :--: |
|       ��ʼ��ֵ        | left = 0, right = numsSize | left = 0, right = numsSize | ��ͬ |
|       while����       |        left < right        |        left < right        | ��ͬ |
| `nums[mid] < target `|       left = mid + 1       |       left = mid + 1       | ��ͬ |
| `nums[mid] > target ` |        right = mid         |        right = mid         | ��ͬ |
| `nums[mid] == target` |        right = mid         |       left = mid + 1       | ��ͬ |
|        ����ֵ         |            left            |          left - 1          | ��ͬ |




## ʵս��ϰ
---
ͨ��ʵս��ϰ�����Լ�����ʵ�֣����ܼ����Ƿ���⵽λ���Ƽ�������ϰ��
 - ��ϰʹ��ϵͳ�⺯��
 - ���߿ɳ�������ʵ�ֱ�����Ķ��ַ�����
 - �ܽ�������Ӧ�����Ҳ�߽����
 
 **����������ֲ��Ҵ���**ʵ�ֹ��ο���
 
```c
int search(int* nums, int numsSize, int target){
    if (nums == NULL) return -1;
    int left = 0;
    int right = numsSize - 1; // ����ұ�����

    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        }
    }
    return -1; // ������δ�ҵ�
}
```

 **���LeetCodeϰ�⣺**

**`��ֵ`**
 - [704.���ֲ��ң��򵥣�](https://leetcode-cn.com/problems/binary-search/)
 - [633. ƽ����֮��](https://leetcode-cn.com/problems/sum-of-square-numbers/)
 - [69. x ��ƽ����](https://leetcode-cn.com/problems/sqrtx/)
 - [��ָ Offer 53 - II. 0��n-1��ȱʧ������](https://leetcode-cn.com/problems/que-shi-de-shu-zi-lcof/)


**`����߽�`**

 - [875.�����㽶�����棨�еȣ�](https://leetcode-cn.com/problems/koko-eating-bananas/)
 - [658. �ҵ� K ����ӽ���Ԫ��](https://leetcode-cn.com/problems/find-k-closest-elements/)
 - [275. H ָ�� II](https://leetcode-cn.com/problems/h-index-ii/)
 - [1300. ת���������ӽ�Ŀ��ֵ�������](https://leetcode-cn.com/problems/sum-of-mutated-array-closest-to-target/)
 - [410. �ָ���������ֵ](https://leetcode-cn.com/problems/split-array-largest-sum/)
 - [35. ��������λ��](https://leetcode-cn.com/problems/search-insert-position/)
 - [1011.��D�����ʹ�������������еȣ�](https://leetcode-cn.com/problems/capacity-to-ship-packages-within-d-days/)

**`���ұ߽�`**

 - [1712. ������ֳ�����������ķ�����](https://leetcode-cn.com/problems/ways-to-split-array-into-three-subarrays/)
 -  [1552.����֮��Ĵ���](https://leetcode-cn.com/problems/xiao-zhang-shua-ti-ji-hua/solution/)
 -  [34.�����������в���Ԫ�صĵ�һ�������һ��λ�ã��еȣ�](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)
 - [���俳������](https://oi-wiki.org/basic/binary/)

## �ο�����
---
1. [��д����ʫ����������۾�Ҳ��д�Զ�������](https://labuladong.gitbook.io/algo/mu-lu-ye/er-fen-cha-zhao-xiang-jie)
2. [����������ô�ã������ܽ�����·](https://labuladong.gitbook.io/algo/mu-lu-ye-1/mu-lu-ye-3/er-fen-yun-yong)
3. [�ҺͿ������ԹٶԶ����������������̽��](https://labuladong.gitbook.io/algo/mu-lu-ye-1/mu-lu-ye-3/er-fen-fen-ge-zi-shu-zu)
4. [C ��������bsearch()ʵ�ֲ��Ҳ���](https://www.cnblogs.com/darklights/p/5022505.html)
5. [����Ӧ�ã����������Ӻܾ��䡿](https://oi-wiki.org/basic/binary/)


