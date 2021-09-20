# C刷题：找两个数之间位数为偶数的个数
**目录**
[toc]


**Git项目地址**：[LeetCodeUsingC刷题笔记](https://github.com/ioMayday/leetcode_sol)

## 题目描述
---
**题目：**
> 给定正整数N、M，均介于1~10^9之间，N<=M，找出两者之间(含N、M)的位数为偶数的数有多少个。

**测试用例：**

> 输入：N = 1, M = 100 
> 输出：90 
> 样例说明：[1,100]区间上，有10~99元素的位数是2位，是偶数，这些整数的数量为90。

> 输入：N = 1, M = 1000 
> 输出：91 
> 样例说明：在上个样例结果上，加上一个1000这个位数为偶数的数，即90 + 1 = 91。

## 分析
----
数据输入小于`2^31=2,147,483,648`,可以用int来保存。

### 方法一
该方法是最容易想到的方法，易推广。

- 得到n，m的位数
- 若n，m位数相同：
	- 为偶数，则返回 `m - n + 1`
	- 为奇数，则返回0
- 若n，m位数不同：
	- 分别判断起止区间n和m位数
		- 若n的位数为偶数，则 `位数的末端数 - n + 1`；如位数是`2`，则 `99 - n + 1` (`99 = 10^2 - 1`)
		- 若m的位数为偶数，则 `m - 位数的起始数 + 1`；如位数是`2`，则 `m - 10 + 1` (`10 = 10^(2 - 1)`)
		- 其他情况，则不处理
   - 从n的位数+1遍历到m的位数-1
		- 若位数为偶数，则直接乘对应整段区间数

### 方法二
该方法代码精简，思路清晰。
 - 10^9范围有限，直接划分出位数为偶数的区间 
 - 遍历所有区间，检测n,m落在这些区间的情况 
 - 找到m归属情况后可退出遍历

## C代码
----
方法一：

```c
#define BASE_OCT    10
#define STEP        2
#define EVEN_BASE   2
int GetBitsNum(int val)
{
    int bit = 0;
    while (val != 0) {
        val /= BASE_OCT;
        bit++;
    }
    return bit;
}

int mostDouble2(int n, int m)
{
    int even = 0;
    int bitN = GetBitsNum(n);
    int bitM = GetBitsNum(m);

    if (bitN == bitM) {
        if (bitN % 2 == 0) {
            return m - n + 1;
        }
        return 0;
    }

    if (bitN % EVEN_BASE == 0) {
        even += (int)pow(10, bitN) - 1 - n + 1;   // 9999 = 10000 - 1
    } else {
        bitN++; // 调整为偶数起
    }
    if (bitM % EVEN_BASE == 0) {
        even += m - (int)pow(10, bitM - 1) + 1; 
    }

    int i;
    for (i = bitN; i < bitM; i += STEP) {
        even +=  (int)pow(10, i) - 1 - (int)pow(10, i - 1) + 1;
    }
    return even;
}
```

方法二：
```c
#define ROW 5
#define COL 2
const int g_evenRanges[ROW][COL] = {
    {10, 99},
    {1000, 9999},
    {100000, 999999},
    {10000000, 9999999},
    {1000000000, 1000000001},
};

int mostDouble2(int n, int m)
{
    int even = 0;
    int i;
    for (i = 0; i < ROW; i++) {
        // n, m落在区间内
        if (n >= g_evenRanges[i][0] && m <= g_evenRanges[i][1]) {
            return even = m - n + 1;
        }
        // n 落在区间左侧
        if (n >= g_evenRanges[i][0] && 
            n <= g_evenRanges[i][0] && m > g_evenRanges[i][1]) 			{
            even += g_evenRanges[i][1] - n + 1;
        }
        // n, m 中间的区间数累加
        if (n < g_evenRanges[i][0] && m > g_evenRanges[i][1]) {
            even += g_evenRanges[i][1] - g_evenRanges[i][0] + 1;
        }
        // m 落在区间右侧
        if (n < g_evenRanges[i][0] && 
            m >= g_evenRanges[i][0] && m <= g_evenRanges[i][1]) {
            even += m - g_evenRanges[i][0] + 1;
            break; // 已检测完[n, m]范围
        }
    }
    return even;
}
```


## LeetCode相关题目
---
[1295. 统计位数为偶数的数字 （简单）](https://leetcode-cn.com/problems/find-numbers-with-even-number-of-digits/)

实现思路：

 - 直接遍历数组，循环调用方法一中的`GetBitsNum()`函数
 - 记录调用结果能被2整除的次数即可


