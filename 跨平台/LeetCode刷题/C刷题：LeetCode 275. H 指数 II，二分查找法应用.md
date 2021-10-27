**����Ŀ¼**
[toc]

���⣺Cˢ�⣺LeetCode 275. H ָ�� II�����ֲ��ҷ�Ӧ��

> ���ߣ���֪��
> ���ںţ���֪��
> ˢ�⽻��QQȺ��444172041

## ��Ŀ����

------



> **����˵��**��
>
> ����һ���������� citations ������ citations[i] ��ʾ�о��ߵĵ� i ƪ���ı����õĴ�����citations �Ѿ����� �������� �����㲢���ظ��о��ߵ� h ָ����
>
> h ָ���Ķ��壺h ���������ô�������high citations����һ��������Ա�� h ָ����ָ���������� ��n ƪ�����У��ܹ��� h ƪ���ķֱ����������� h �Ρ�������� n - h ƪ����ÿƪ�����ô��� ������ h �Ρ�
>
> **��ʾ**����� h �ж��ֿ��ܵ�ֵ��h ָ�� �����������Ǹ���Ҫ��ʱ�临�ӶȲ�����`O(logN)`
>
> **��Ŀ����**��[275. H ָ�� II���е�](https://leetcode-cn.com/problems/h-index-ii/)



## ˼·����

--------



�����Ķ���������⣬�������£�

- ���ļ�`������<=������`
- �±�idx��������Ϊcita[idx]�������idx�������������n
- ���� `n <= cita[idx]`����`n`������Ϊ�����е�һ��hָ��ֵ


�ɵõ�����˼·�����£�

**˼·1**

- �Ӻ���ǰ���ҵ���һ�����������ģ���Ϊ���Hֵ
- ʱ�临�Ӷ�`O(N)`

**˼·2**

- ��ʵ��������Ĳ��ҵ���ȷ��hֵ
  - ����`n <= cita[idx]`���˳����ֲ���
- ���Ż�������ȷ��ֵ�й�����������Hֵ
  - Hֵ���ʱ���������е��±���С
  - ���ҵ�ֵ���˳���һֱ�����ұ߽�
  - �Ӷ��ҵ���������ʱ�����ֵ
- ʱ�临�Ӷȣ�`O(logN)`

## ����ʵ��

--------

˼·1ʵ�ֽ�Ϊ�򵥣�����������ĿҪ�������Թ����ص��о�˼·2��

��ʵ���ҵ�Hֵ�Ļ������ִ��룺

```c
int hIndex(int* citations, int citationsSize)
{
    int left = 0;
    int right = citationsSize;
    while (left < right) {
        int mid = left + (right - left) / 2;
        int tmpH = citationsSize - mid;

        // the condition that citations[mid - 1] <= tmpH satisfies simultaneously
        // becasue of the ascending order array
        if (citations[mid] >= tmpH) {
            return citationsSize - mid; // >=, include mid
        } else {
            left = mid + 1;
        }
    }
    return left;
}

```

���Ż����ҵ�Hֵ�����ֵ�������ִ��룺

```c
int hIndex(int* citations, int citationsSize)
{
    int left = 0;
    int right = citationsSize;
    while (left < right) {
        int mid = left + (right - left) / 2;
        int tmpH = citationsSize - mid;

        // the condition that citations[mid - 1] <= tmpH satisfies simultaneously
        // becasue of the ascending order array
        if (citations[mid] >= tmpH) {
            right = mid; // shrink right range
        } else {
            left = mid + 1;
        }
    }
    return citationsSize - right; // origin: citationsSize - mid
}
```

�����в��Ե�����demo��

```c
int main(void)
{
    int ret;
    int cita1[5] = {0, 1, 3, 5, 6};
    ret = hIndex(cita1, 5);
    printf("ret = %d\n", ret); // 3

    int cita2[3] = {1, 2, 100};
    ret = hIndex(cita2, 3);
    printf("ret = %d\n", ret); // 2

    int cita3[2] = {0, 2};
    ret = hIndex(cita3, 2);
    printf("ret = %d\n", ret); // 1

    int cita4[1] = {0};
    ret = hIndex(cita4, 1);
    printf("ret = %d\n", ret); // 0

    return 0;
}
```

�ύ����ִ����ʱ������30%���ύ����ʱ20ms��Ѱ˼�ѵ����и���ķ������о�����ִ����ʱ���ٵĴ���ʵ�����£�����ʱ�临�Ӷ���Ȼ��`O(logN)`��������ʵ�ֲ�����ǳ���2���õ�����λ��

```c
int lowerBound(const int* nums, int len) {
    int low = 0, high = len-1;
    while (low < high) {
        int mid = (low+high) >> 1; // different
        if (len-mid > nums[mid]) {
            low = mid + 1;
        } else {
            high = mid;
        }
    }
    
    return low;
}

int hIndex(int* citations, int citationsSize){
    int n = citationsSize, idx = lowerBound(citations, n);
    return citations[idx] ? n-idx : 0;
}

```

���ԣ��ó����ۣ�LeetCode��ִ����ʱ�����ͺã�����׼ȷ���ص���㷨ʵ�ֵ�ʱ�临�ӶȽ��ͼ��ɡ����⣬δ�Ż��Ļ������ִ����У����ڵ����������У�

- �������`citations[mid] >= tmpH`������ͬʱ�ͱ�֤`citations[mid - 1] <= tmpH`
- ǰ�߲����ܱ�֤���ں���������������tmpH
- ֻ��˵ֻ���ҵ�һ��hֵ�����hֵ�����С����ȷ��



## �ο�����

------

�ޡ�