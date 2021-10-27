
**目录**
[toc]

标题：C学习：C中sizeof的用法

> 作者：来知晓
> 公众号：来知晓
> 简介：哈工大硕士，华为算法工程师，专注分享读书、职场、技术及自我提升等内容


> 附：[C代码在线调试工具](https://c.runoob.com/compile/11)
## 简述
`sizeof`是C语言的一种**单目操作符**，如C语言的其他操作符`++、--`等，并不是函数。`sizeof`操作符以字节形式给出了其操作数的存储大小，即**单位为Byte**。

## 用于数据类型
```c
sizeof(int)  		// 结果为4，单位为byte
sizeof(double)		// 结果为8
```

## 用于变量
```c
int a[10];
double b[10];
int tmp1 = sizeof(a);
int tmp2 = sizeof(a) / sizeof(int);
int tmp3 = sizeof(b);
int tmp4 = sizeof(b) / sizeof(double);
```


以上代码运行结果为
```c
tmp1 = 40， 表示a数组占40个byte存储空间
tmp2 = 10， 表示a数组有10个int元素
tmp3 = 80， 表示b数组占40个byte存储空间
tmp4 = 10， 表示b数组有10个double元素
```



## 用于指针
```c
int a[10];
int *p = a;
int tmp5 = sizeof(p);
int tmp6 = sizeof(*p);
```

以上代码运行结果为

```c
tmp5 = 4 or 8，  32位系统时指针存储大小单位为4，64位时则位8
tmp6 = 4,        *p为int类型，大小占4字节
```

## sizeof应用举例
----
实例1：
```c
int arr[] = {0, 2, 3, 4, 5, 6, 7, 8, 1, 7};
char str[] = "Hello";
int lenArr = sizeof(arr) / sizeof(int);
int lenStr1 = sizeof(str);
int lenStr2 = strlen(str);
```
结果：
```c
> lenArr=10 
> lenStr1=6 
> lenStr2=5
```

分析：
 - 因为`sizeof`返回的单位是Byte，故arr数组有多少个int数据，要先除以单个int的大小，不能直接用sizeof(arr)。
 - `sizeof(str)` 长度为6，包涵结尾符'\0'，所以缓存字符数组空间申请时一定要预留 '\0'；
 - `strlen(str)` 长度为5，到'\0'结束，结果不含'\0'。
--------
实例2：
```c
int nums[3] = {0, 1, 2};
int size1 = sizeof(nums);
int RepeatNum1(int *nums, int len)
{
    int size2 = sizeof(nums);
    qsort(nums, len, sizeof(int), cmp);
}
```
- size1结果为`3 * 4 = 12`
- size2结果为4（32位系统），第二个数组在传参后退化为指针，取的是指针地址大小。64位系统指针地址大小为8。

**注意：**
sizeof用于结构体/联合体等，要考虑地址对齐的问题。


 
**参考链接：**
[https://www.cnblogs.com/ttltry-air/archive/2012/08/30/2663366.html](https://www.cnblogs.com/ttltry-air/archive/2012/08/30/2663366.html)




