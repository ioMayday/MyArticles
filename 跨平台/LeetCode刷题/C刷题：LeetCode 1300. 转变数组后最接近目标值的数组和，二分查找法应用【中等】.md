**Ŀ¼**
[toc]

���⣺Cˢ�⣺LeetCode 1300. ת���������ӽ�Ŀ��ֵ������ͣ����ֲ��ҷ�Ӧ�á��еȡ�

> ���ߣ���֪��
> ���ںţ���֪��
> ˢ�⽻��QQȺ��444172041

## ��Ŀ����

------



> **����˵��**��
>
> ����һ���������� arr ��һ��Ŀ��ֵ target �����㷵��һ������ value ��ʹ�ý����������д��� value ��ֵ��� value ������ĺ���ӽ�  target ����ӽ���ʾ����֮��ľ���ֵ��С����
>
> ����ж���ʹ�ú���ӽ� target �ķ��������㷵����Щ�����е���Сֵ��
>
> ��ע�⣬�𰸲�һ���� arr �е����֡�
>
> **���ݷ�Χ��**
>
> - `1 <= arr.length <= 10^4`
> - `1 <= arr[i], target <= 10^5`
>
> **��Ŀ����**��[1300. ת���������ӽ�Ŀ��ֵ�������](https://leetcode-cn.com/problems/sum-of-mutated-array-closest-to-target/)

���ʣ�ɶ����£�value���ж��ֵ��

## ˼·����

--------



�����Ķ���������⣬�������£�

- Ŀ�����ҵ�һ����С��value
- �����������д���value��ֵ�������value
- ʹ����ͼ�С��Ȼ�����������ӽ�target
- �д˵�ʽ��`value<=target` �� `value<=maxVal`����`value >= 0 `
- ������������Ŀ��֤��һ���н��

����֪����һ��Ҫ��ĳ���鲻ͬ������ֵ��ʱ��һ��Ҫ�뵽ǰ׺�����顣һ��Ҫ�ö��ַ�ʱ����������򣬴Ӷ�ʹ�󲿷ִ���value��ֵ����Ѹ�ٵõ������

���ǣ��ɵ�˼·���£�

- ��qsort�����ٶ�������ǰ׺��

- �������������sum��target�Ĺ�ϵ

  - �� `sum < target`���޽�, �ҵ�һ��֮��
  
  - �� `sum = target`����С��value�����������ֵ

  - �� `sum > target`����������˼·

    - ˼·һ���鿴��ֵ�����ö������٣� pass��

    - ˼·�������ַ����
      - value��0��ʼ����Ӧ��sum��С
      - value�ۼӵ�maxVal����Ӧ��sum���
      - �����ҵ��м�ֵ ok�� ����while�жϣ���Ƚ�right��right-1��Ӧ��sum

- ʱ�临�Ӷȣ�`O(NlogN)`����qsort���Ƶ�



## ����ʵ��

--------



����֪����������Ϊ��`10^4 * 10^5 = 10^9`��������С��Ϊ `1`������int���ʹ洢����ͽ��������������Զ������ҵ����н⣬��������ֵ��

����汾���£�

```c
#define MAX_NUM_SIZE 10001

int Compar(void *a, void *b)
{
    return *(int *)a - *(int *)b; // ascending order
}

int GetIdx(int* arr, int arrSize, int target)
{
    int i;
    for (i = 0; i < arrSize; i++) {
        if (arr[i] > target) {
            return i;
        }
    }
    // todo
    // int left = 0;
    // int right = arr[arrSize - 1];
    // while (left < right) {
    //     int mid = left + ((right - left) >> 1);
    //     int curSum = mid;
    // }
    return -1; // not found
}

int findBestValue(int* arr, int arrSize, int target)
{
    // sort and get preSum
    qsort(arr, arrSize, sizeof(int), Compar);
    int preSum[MAX_NUM_SIZE] = {0};
    int i;
    for (i = 1; i < arrSize + 1; i++) {
        preSum[i] = arr[i - 1] + preSum[i - 1];
    }

    // process data
    int res = preSum[arrSize] - target;
    if (res <= 0) {
        return arr[arrSize - 1];
    }

    int curIdx, midSum, preeSum;
    int left = 0;
    int right = arr[arrSize - 1] + 1;
    while (left < right) {
        int mid = left + ((right - left) >> 1);
        curIdx = GetIdx(arr, arrSize, mid); // first idx which is greater than mid
        if (curIdx == -1) {
            res = mid;      // last time is ok, this time sum must greater than target
            break;          // so right is mid, and end while, compare right and right - 1
        }
        midSum = (arrSize - curIdx) * mid;
        preeSum = preSum[curIdx];
        int curSum = preeSum + midSum;
        int diff = curSum - target;
        if (diff == 0) {
            return mid;
        } else if (diff < 0) {
            left = mid + 1;
        } else if (diff > 0) {
            right = mid;
        }
    }

    // if left == right, then end while
    if (right <= 0) {
        return right;
    }

    int lSum, rSum, absDiffL, absDiffR;
    // break while contidon
    if (left != right && left > 0) { // compare res and left - 1
        rSum = preSum[arrSize];
    } else {  // normal end while condition
        curIdx = GetIdx(arr, arrSize, right);
        midSum = (arrSize - curIdx) * right;
        preeSum = preSum[curIdx];
        rSum = preeSum + midSum;
    }
    left = left - 1;
    curIdx = GetIdx(arr, arrSize, left);
    midSum = (arrSize - curIdx) * left;
    preeSum = preSum[curIdx];
    lSum = preeSum + midSum;

    absDiffL = abs(lSum - target);
    absDiffR = abs(rSum - target);
    return (absDiffL <= absDiffR) ? left : right;
}
```



������demo���£�

```c
int main(void)
{
    int ret;
    int arr[3] = {4,9,3};
    ret = findBestValue(arr, 3, 10); // 3
    printf("ret = %d\n", ret);

    int arr1[3] = {2,3,5};
    ret = findBestValue(arr1, 3, 10); // 5
    printf("ret = %d\n", ret);

    int arr2[5] = {60864,25176,27249,21296,20204};
    ret = findBestValue(arr2, 5, 56803); // 11361
    printf("ret = %d\n", ret);

    int arr3[3] = {1,1,1};
    ret = findBestValue(arr3, 3, 3); // 1
    printf("ret = %d\n", ret);

    int arr4[5] = {48772,52931,14253,32289,75263};
    ret = findBestValue(arr4, 5, 40876); // 8175
    printf("ret = %d\n", ret);

    int arr5[2] = {2,2};
    ret = findBestValue(arr5, 2, 3); // 1
    printf("ret = %d\n", ret);

    return 0;
}
```



���**������**�����뻹�����Ż��𣬻��ܸ����ǿЩ��

## �ο�����

------



1. [�ٷ���⣺1300. ת���������ӽ�Ŀ��ֵ�������](https://leetcode-cn.com/problems/sum-of-mutated-array-closest-to-target/solution/)



