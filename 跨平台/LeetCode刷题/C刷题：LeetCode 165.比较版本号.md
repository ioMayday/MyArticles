# C刷题：LeetCode 165.比较版本号
[toc]

## 前言
---

**Git项目地址**：[LeetCodeUsingC刷题笔记](https://github.com/ioMayday/leetcode_sol)

可能因为C语言过于底层，许多方便数据结构和库函数都没有，发现网上**用C代码刷LeetCode的项目并不多**。

为真正锻炼代码能力，我们还是从底层轮子熟悉起吧，自己造一下试试，以后才能高屋建瓴，后面用其他语言实现时，调用也能更得心应手。

这**系列博客**不会把刷的每道LeetCode题都拿出来分析，**仅针对卡壳较久、有些debug技巧的题目**，分享出来，让大家少入些坑。更多题目解法参考Git项目：[LeetCodeUsingC刷题笔记](https://github.com/ioMayday/leetcode_sol)
。

## 知识点
---
 - 字符分割函数`strtok()`用法
 - 字符转整数`atoi()`用法
 - 特殊用例的处理

## 题目分析
----
题目链接：[LeetCode 165.比较版本号](https://leetcode-cn.com/problems/compare-version-numbers/)，题目中**输入**为两个字符串，一个是`版本号1`，一个是`版本号2`，让判断两个版本号的高低，**输出**用`0/1/-1`作为相同、高、低的标志。

**字符串特点：**
 - 用 `.` 作为分割符
 - 前置`0`无效
 - 【特殊用例】在前面版本号相同时，`.` 后的`全是0`则无效，有非0的则长度有效

## 主要思路
---

 - 先把两个字符串的各自分割转为数组，并记录数组长度
 - 遍历较小长度的数组，比较数值，一旦出现不等，则可以返回`1/-1`
 - 以较小长度的数组的数值若都判断全相等，则判断：
   - 若为字符串特点第3点的全0无效情况，则返回0
   - 否则长数组的长度有效，长度大的版本高

## 代码开销
---
> 执行用时： **0 ms** , 在所有 C 提交中击败了 100.00% 的用户 <br>
> 内存消耗： **5.5 MB** , 在所有 C 提交中击败了  54.51% 的用户

## 实现代码
---
C代码版本如下：

```c
int SplitStr(char *version, const char *split, int *verNum, int len)
{
    char *p = strtok(version, split);
    while (p) {
        verNum[len++] = atoi(p);
        p = strtok(NULL, split);
    }
    return len;
}

int compareVersion(char * version1, char * version2)
{
    // 切割ver1和ver2，放到数组，并记录长度
    int ver1[500] = {0};
    int ver2[500] = {0};
    int lenVer1 = 0, lenVer2 = 0;
    const *split = ".";
    lenVer1 = SplitStr(version1, split, ver1, lenVer1);
    lenVer2 = SplitStr(version2, split, ver2, lenVer2);
    
    // 比较数组
    int minLen = lenVer1 < lenVer2 ? lenVer1 : lenVer2;
    int i;
    for (i = 0; i < minLen; i++) {
        if (ver1[i] > ver2[i]) {
            return 1;
        } else if (ver1[i] < ver2[i]) {
            return -1;
        }
    }
    // 解决下类用例
    // "1.0"
    // "1.0.0"
    // 判断长版本号，后续是否都为0
    if (minLen == lenVer1) { // 长的是lenVer2
        for (i = lenVer1; i < lenVer2; i++) {
            if (ver2[i] != 0) {
                break;
            }
        }
        if (i == lenVer2) { // 长版本号后续全为0
            return 0;
        }
    } else {
        for (i = lenVer2; i < lenVer1; i++) {
            if (ver1[i] != 0) {
                break;
            }
        }
        if (i == lenVer1) { // 长版本号后续全为0
            return 0;
        }
    }

    // 最小长度数组退出且前面break
    // 即每个号都相同且长版本号后续不全为0
    // 比较段数
    if (lenVer1 > lenVer2) {
        return 1;
    } else if (lenVer1 < lenVer2) {
        return -1;
    }
    // lenVer1 == lenVer2)
    return 0;  // 长度相等
}
```
