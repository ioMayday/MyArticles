
**目录**
[toc]

> 作者：来知晓
> 公众号：来知晓
> 简介：哈工大硕士，华为算法工程师，专注分享读书、职场、技术及自我提升等内容


标题：C学习：整型提升规则辨析


## 定义
----
不同数据类型在混合运算时，时常会进行整型提升或者强制转换的操作，C语言中提升规则如下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108151854178.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE3MjU2Njg5,size_16,color_FFFFFF,t_70#pic_center)

## 说明
-----

 - 当short或者char类型数据进行混合运算时，统一都会转成int类型，根据左值的数据类型再做转换，所以一般不会有溢出或者截断现象。
 - float/double类型向整型转换时，直接截断取整，去掉小数点后的数据。

## 练习
----
### 题目1

下面两个if判断结果如何？

```c
unsigned int a = 20;
int b = 13;
int k = b - a;
if (k > b + a) {
    printf("k > b + a   Ok\n");
}
if (k == b - a) {
    printf("k == b - a   Ok");
}
int b = 13;
```
**答案：**
两个if结果均为真。

**分析：**
 - k是存的-7，b - a时，b提升为unsigned类型与a相减，得到的是一个较大的unsigned数，之后赋给k后强转为int，值为-7
 - k > b + a 比较时，左右两边提升为`unsigned`，k存的负数被解析成为一个很大的正数，肯定大于 `b + a` 的结果33
 - k == b - a 两边都是存的较大的unsigned正数，在int类型中为-7
 - int类型的-7在计算机存储的值为 `0xfff9`，如果解析为有符号数则为-7，解析为无符号数则为`65529`
 - int类型默认为`signed int`

	
### 题目2
下面代码输出的结果各为多少？
```c
unsigned int t = -1;
//上句等价于
//unsigned int t = 0xffffffff;
unsigned int a = 10;
int b = -13;
int c = a + b;

printf("%d\n", t); 
printf("%ud\n", (a+b));  
printf("%d\n", (a+b));  
printf("%d\n", c);
```

**答案：**
```c
-1            // unsigned int 转int不扩展，按signed解析为-1
4294967293d   // d说明是用十进制表示的
-3
-3
```

**分析：**

```c
printf("%d\n", (a+b));
printf("%ud\n", (a+b));  
```
 - %d，表示默认按signed int类型输出，不管右边变量是short/char还是有无符号，最后都会扩展为signed int
 - %ud，则表示将右边变量强制按无符号整型unsigned int输出
 - %ld，则表示用long int类型输出

### 题目3
下面代码输出的结果分别是?
```c
signed short i0 = 65535; // 0xffff
unsigned short i1 = 65535; 
printf("%d \n", i0);
printf("%d \n", i1);
```

**答案：**
```c
-1
65535
```

**分析：**

 - 不管是有符号还是无符号的short类型，%d输出时，统一都是按signed int输出。
 - 所以第一个0xffff按符号位扩展，被扩展为0xffffffff，输出-1；
   第二个0xffff，按无符号位方式扩展，被扩展为0x0000ffff，输出65535.


### 题目4
下面代码输出的结果分别是?
```c
signed char i0 = 255; // 0xff
unsigned char i1 = 255; 
printf("%d \n", i0);
printf("%d \n", i1);
```
**答案：**
```c
-1
255
```
**分析：**
类似的，255的存储值是0xff，用signed char表示是-1，所以扩展成int时，因为是负，所以全填充1。而unsigned char扩展时，无符号位全填充0，所以按signed int输出时是255.

下面代码输出的结果分别是?
```c
signed char i0 = 0xff;
unsigned char i1 = 0xff;  
// 下面两句与上一句等价
// unsigned char i1 = -1;   // 因为-1实质存储值就是0xff
// unsigned char i1 = 255;
printf("%d \n", i0);
printf("%d \n", i1);
```
**答案**
```c
-1
255
```
**分析：**
0xff可以解析为-1，也可以解析为255。具体看数据类型是否为有符号数。
