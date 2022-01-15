**目录**
[toc]

标题：C学习：一个负数补码把我整蒙了

> 附：[C代码在线调试工具](https://c.runoob.com/compile/11)

## 负数补码实质
---
**负数补码定义**参考之前讨论的一篇博客：[C学习：有符号数类型的负数在计算机中的存储](https://blog.csdn.net/qq_17256689/article/details/109559494)

正数的补码为其本身，负数的补码求取方式有两种。

`方法一`，人工求取步骤如下：
 - 取绝对值，得对应正数
 - 对正数的二进制形式按位取反
 - 将上面结果加1，得到补码

`方法二`，从原码角度来操作，如-32，求取步骤如下：
 - 原码为：10100000 
 - 最高位不变，其余取反：11011111 
 - 加一得补码：11100000

**方法一**过程为负数取绝对值，按位取反，再加1。**方法二**过程为原码的最高位不变，其余取反，然后加一。**本质**与方法一步骤一样，**建议**只用记第一种策略就行。

**重要提示**就是负数补码本质，`计算机存负数时本身就已自动转换成补码`，不需要单独再去转换。但是，如果不清楚的话，会仍在计算机存储后负数后继续对该变量人工求补码，这样就会出问题了。

**再次强调**下，负数在计算机里存储时，如 `int num = -1`; num本质已被转化成补码了，要想拿到补码，直接强制转成无符号数表达，其二进制就是补码。不信可以`print`以下步骤看看。

## 代码示例分析
---
假设输入`num = -10`，取消注释即可debug。
```c
if (num < 0) { 					// 人工转换求取负数的补码
    // 逐步debug发现，取反位操作后，与预期不符，说明是在对本身负数存的补码在操作
    // int val = (int)num; 		// 强制转成32位的
    // printf("0x%x\n", val);  	// 0xfffffff6
    // val = -val;  			// 符号位变正
    // printf("0x%x\n", val);  	// 0xa
    // val = ~val;  			// 负数的模，除符号位外按位取反
    // printf("0x%x\n", val);  	// 0xfffffff5
    // val = val & 0x7FFFFFFF; 	// 把符号位变成正
    // printf("0x%x\n", val); 	// 0x7ffffff5
    // val = val + 1;      		// 加1成补码，当正数处理
    // printf("0x%x\n", val); 	// 0x7ffffff6
    // num = val; 				// 此步结果，不是正确的补码值

    // 负数本质在计算机里已被转化成补码了，要想拿到补码，直接强制转成无符号数表达，其二进制就是补码
    unsigned int val = (unsigned int)num;
    // printf("0x%x\n", val);     // 0xfffffff6， 补码时符号位一直为1，其他位则是在正数模的基础上取反加1
    num = val;  // 赋给num做正数处理
}
```

## 问题来源
---
该问题来自一个题目解答时遇到，题目描述为：将一个输入仅为数字的字符串，根据大小端顺序，分别将值用4字节打印出来。

## 问题分析
---
思路分析如下：
  1. 先字符串转换成数字，大于范围的直接返回`overflow`
  2. 范围内的，先判别是否有负号
  3. 正数按16转进制成字符，小端时每隔两个交换

实现代码如下：
```c
#include <stdio.h>
#include <stdlib.h>
#include <math.h>

#define MAX_STR_LEN 512
// TestCase
// 1234567890123456789012345678900  
// 4027691818
// -10
// 0
// 2^32 = 4294967296     || 减1
// -2^31 = -2147483648   || 减1
void GetHexString(const char *inputStr, char *output, int outputSize)
{
    long long num = atoll(inputStr); // long 和 int 实际范围一致，必须要用atoll
    char overFlow[] = "overflow";
    char *p = overFlow;
    char *p1 = output;
    char bigOrder[9] = "00000000";  // 含\0
    char litOrder[9] = "00000000";  // 默认应该都是0，而非FFFFFF...
    int modVal = 0;
    if (num < -1 * (long long)pow(2, 31) || num >= (long long)pow(2, 32)) { // 题目要求的范围，跟INT_MIN，INT_MAX还不太一致
        while (*p) { // 输出overflow
            *p1 = *p;
            p1++;
            p++;
        }
        *p1 = '\0';
        return;
    }
    p1 = &bigOrder[7];
    char *p2 = &litOrder[0];
    if (num < 0) { // 小于0，用补码，只要求到32位
        // 易错点
        unsigned int val = (unsigned int)num;
        num = val;  // 赋给num做正数处理
    }
    // 直接除以16，得到每个值，然后用字节序连起来
    // 注意大于0时，num不能用int或long表达，因为它只能存到2^31-1，题目要求表达到2^32
    while (num > 0) {      // 若num为0，则跳过
        modVal = num % 16; // 默认小端序先存着
        if (modVal >= 10) {
            *p1 = (modVal - 10) + 'A';
            *p2 = *p1;
            p1--;
            p2++;
        } else {
            *p1 = modVal + '0';
            *p2 = *p1;
            p1--;
            p2++;
        }
        num /= 16;
    }

    // 小端每两个内部交换位置
    int i = 0;
    for (i = 0; i < 8; i += 2) {
        char tmp = litOrder[i];
        litOrder[i] = litOrder[i + 1];
        litOrder[i + 1] = tmp;
    }

    // 连接大小端
    p1 = output;
    p = bigOrder;
    while (*p) {
        *p1 = *p;
        p++;
        p1++;
        *p1++ = *p++;

        *p1 = ' ';
        p1++;
    }
    *p1++ = '\n';

    p = litOrder;
    while (*p) {
        *p1 = *p;
        p++;
        p1++;
        *p1++ = *p++;

        *p1 = ' ';
        p1++;
    }
    *p1 = '\0';

    return;
}

int main(void)
{
    char inputStr[MAX_STR_LEN];
    if (scanf_s("%s", inputStr, MAX_STR_LEN) != 1) { return -1; }

    char output[MAX_STR_LEN];
    GetHexString(inputStr, output, MAX_STR_LEN);
    printf("%s", output);

    while (1) { ; }

    return 0;
}
```
