
**Ŀ¼**
[toc]

���⣺Cѧϰ��C��sizeof���÷�

> ���ߣ���֪��
> ���ںţ���֪��
> ��飺������˶ʿ����Ϊ�㷨����ʦ��רע������顢ְ������������������������


> ����[C�������ߵ��Թ���](https://c.runoob.com/compile/11)
## ����
`sizeof`��C���Ե�һ��**��Ŀ������**����C���Ե�����������`++��--`�ȣ������Ǻ�����`sizeof`���������ֽ���ʽ��������������Ĵ洢��С����**��λΪByte**��

## ������������
```c
sizeof(int)  		// ���Ϊ4����λΪbyte
sizeof(double)		// ���Ϊ8
```

## ���ڱ���
```c
int a[10];
double b[10];
int tmp1 = sizeof(a);
int tmp2 = sizeof(a) / sizeof(int);
int tmp3 = sizeof(b);
int tmp4 = sizeof(b) / sizeof(double);
```


���ϴ������н��Ϊ
```c
tmp1 = 40�� ��ʾa����ռ40��byte�洢�ռ�
tmp2 = 10�� ��ʾa������10��intԪ��
tmp3 = 80�� ��ʾb����ռ40��byte�洢�ռ�
tmp4 = 10�� ��ʾb������10��doubleԪ��
```



## ����ָ��
```c
int a[10];
int *p = a;
int tmp5 = sizeof(p);
int tmp6 = sizeof(*p);
```

���ϴ������н��Ϊ

```c
tmp5 = 4 or 8��  32λϵͳʱָ��洢��С��λΪ4��64λʱ��λ8
tmp6 = 4,        *pΪint���ͣ���Сռ4�ֽ�
```

## sizeofӦ�þ���
----
ʵ��1��
```c
int arr[] = {0, 2, 3, 4, 5, 6, 7, 8, 1, 7};
char str[] = "Hello";
int lenArr = sizeof(arr) / sizeof(int);
int lenStr1 = sizeof(str);
int lenStr2 = strlen(str);
```
�����
```c
> lenArr=10 
> lenStr1=6 
> lenStr2=5
```

������
 - ��Ϊ`sizeof`���صĵ�λ��Byte����arr�����ж��ٸ�int���ݣ�Ҫ�ȳ��Ե���int�Ĵ�С������ֱ����sizeof(arr)��
 - `sizeof(str)` ����Ϊ6��������β��'\0'�����Ի����ַ�����ռ�����ʱһ��ҪԤ�� '\0'��
 - `strlen(str)` ����Ϊ5����'\0'�������������'\0'��
--------
ʵ��2��
```c
int nums[3] = {0, 1, 2};
int size1 = sizeof(nums);
int RepeatNum1(int *nums, int len)
{
    int size2 = sizeof(nums);
    qsort(nums, len, sizeof(int), cmp);
}
```
- size1���Ϊ`3 * 4 = 12`
- size2���Ϊ4��32λϵͳ�����ڶ��������ڴ��κ��˻�Ϊָ�룬ȡ����ָ���ַ��С��64λϵͳָ���ַ��СΪ8��

**ע�⣺**
sizeof���ڽṹ��/������ȣ�Ҫ���ǵ�ַ��������⡣


 
**�ο����ӣ�**
[https://www.cnblogs.com/ttltry-air/archive/2012/08/30/2663366.html](https://www.cnblogs.com/ttltry-air/archive/2012/08/30/2663366.html)




