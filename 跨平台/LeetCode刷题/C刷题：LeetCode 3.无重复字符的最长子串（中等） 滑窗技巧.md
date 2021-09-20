# C刷题：LeetCode 3.无重复字符的最长子串（中等） || 滑窗技巧
**目录**
[toc]

> **5.28更新：**
> 看到文章 [关于 labuladong抄的算法题解 这本书的讨论](https://leetcode-cn.com/circle/discuss/MKwnEl/)，大致结论可以得出，该书大部分内容确实是labuladong小抄，并非其原创，希望大家加以分辨了解，并支持真正的原创者。

注：本文当时参考的labuladong的算法小抄的相关思路，代码实现为独立写的C代码。本篇解法参考了[labuladong的C++滑窗模板](https://labuladong.gitbook.io/algo/di-yi-zhang-shou-ba-shou-shua-shu-ju-jie-gou/shou-ba-shou-shua-shu-zu-ti-mu/hua-dong-chuang-kou-ji-qiao-jin-jie)，并根据C代码实现做了部分调整，相关改变见注释。

**Git项目地址**：[LeetCodeUsingC刷题笔记](https://github.com/ioMayday/leetcode_sol)



## 总体思路
---
 - 用滑窗可以在整体一个for循环内解决，关键是滑窗右侧的指针一定要放在最外层for循环中来进行右移，以保证每次子循环能用上之前的查找结果，保证之前的是不重复的。
 - 左侧指针根据扫描结果来决定内循环的起始位置。
 - 用两个指针，外循环当右指针，内循环当左指针，滑窗范围为左右指针闭环范围内，右指针每次右移一个值，都要在左指针范围内判断是否重复字符。
 - 如果是重复字符，则将左指针移至与右指针字符重复的那个左侧字符的下一个，滑窗收缩。
 - 如果不是，则滑窗扩张，滑窗大小要与当前所存最大值比较，如果大于当前最大值，则覆盖，保存最新滑窗大小。

## 方法一
---
labuladong的C实现代码：
```c
/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
// 可改为26，表示26个小写字母，但在相应地方要做 - 'a' 操作
#define MAX_LEN 128 // ascii码个数
#define max(a, b) ((a) > (b) ? (a) : (b))
int lengthOfLongestSubstring(char * s)
{
    if (s == NULL) return 0;

    int hashWindow[MAX_LEN] = {0};
    int start = 0;
    int end = 0;
    int lenS = strlen(s);
    int res = 0;

    // 记录最小覆盖子串的起始索引及长度
    while (end < lenS) {
        // c 是将移入窗口的字符
        char c = s[end];
        // 更新窗内数据
        hashWindow[c]++;
        // 右移窗口
        end++;

        // 左侧窗口收缩
        while (hashWindow[c] > 1) {   // 当前窗内存在重复元素，重复左移，直到移除那个重复元素的值后，跳出
            // d 是将移出窗口的字符
            char d = s[start];
            // 左移窗口
            start++;
            // 进行窗口内数据的一系列更新
            hashWindow[d]--; // 当前窗内含有的键值更新
        }
        // 收缩完得到当前不重复子串的长度，比较后更新
        res = max(res, end - start);
    }

    // 遍历完返回长度
    return res;
}
```

## 方法二
----
滑窗细节实现，右指针在外循环，左指针在内循环：

```c
int lengthOfLongestSubstring(char * s){
    int len_s = strlen(s);
    if (len_s == 0 || len_s == 1) {
        return len_s;
    }

    int curIdx = 0;
    int maxLen = 1;
    int i,j;
    for (i = 1; i < len_s; i++) { //从前往后增长子串
        for (j = i- 1; j >= curIdx; --j) { //每后移一个s[i]，均以当前子串起点间判断是否出现过该字符
            if (s[i] == s[j]) { //判断新出现的s[i]是否跟前面的一样
                curIdx = j + 1; //新子串只能往后移
                break; //跳出内部for循环
            }
            if (maxLen < i - j + 1) {
                maxLen = i - j + 1; //当前的子串长度更长
            }
        }
    }
    return maxLen;
}
```


## 方法三
-----
独立完成的思路，按照惯性思维，左指针在外循环，右指针在内循环。思路较右指针在外循环更容易理解，但是内部逻辑判断较复杂。

```c
int IsRepeatChar(char *s, int left, int right)
{
    int i;
    for (i = left; i < right; i++) {
        if (s[i] == s[right]) {
            return i;
        }
    }
    return -1; //i最小可能为0，输出为负则无对应值
}

int lengthOfLongestSubstring(char * s){
    int len_s = strlen(s);
    if (len_s == 0 || len_s == 1) {
        return len_s;
    }

    int maxLen = 1;
    int i = 0, j = 1, tmp;
    while (i < len_s) {
        for (; j < len_s; j++) {
            tmp = IsRepeatChar(s, i, j);
            if (tmp >= 0) { //找到重复字符
                i = tmp + 1; //左指针移位到重复字符的下一个
                j++; //右指针自然右移到下一位新字符进行判断
                break;
            }
            if (maxLen < j - i + 1) { //无重复字符，且当前滑窗大于最大值
                maxLen = j - i + 1;
            }
        }
        if (j == len_s) { //非中断出来，右指针到末尾
            return maxLen;
        }
    }
    return maxLen;
}
```
