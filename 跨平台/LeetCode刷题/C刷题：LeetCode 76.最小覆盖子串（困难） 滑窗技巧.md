# C刷题：LeetCode 76.最小覆盖子串（困难） || 滑窗技巧
[toc]

> **5.28更新：**
> 看到文章 [关于 labuladong抄的算法题解 这本书的讨论](https://leetcode-cn.com/circle/discuss/MKwnEl/)，大致结论可以得出，该书大部分内容确实是labuladong小抄，并非其原创，希望大家加以分辨了解，并支持真正的原创者。

注：本文当时参考的labuladong的算法小抄的相关思路，代码实现为独立写的C代码。本篇解法参考了[labuladong的C++滑窗模板](https://labuladong.gitbook.io/algo/di-yi-zhang-shou-ba-shou-shua-shu-ju-jie-gou/shou-ba-shou-shua-shu-zu-ti-mu/hua-dong-chuang-kou-ji-qiao-jin-jie)，并根据C代码实现做了部分调整，相关改变见注释。


## 滑窗本质
-----

`滑动窗口本质`就是对暴力解法的两层for循环的优化，也即动态调整内外层的for循环，第一层是left，第二层是right，从逻辑上剪枝去掉多余冗余的判断。

**具体思路**
- 找可行解。从0开始匹配，开启窗口，右边界扩大至满足条件，则此时再往后扩不符合最小子串
- 找最优解。再缩左边界，找到最小子串，然后滑窗。也就是当前从左到右可行解里已找到最优解，那么这段区间后面就没必要再找。
- 滑动窗口。把左边界右移，继续往后滑动，重复右边界扩大至满足条件找新的一个区间
- 终止条件。直到右边界找到末尾停止，返回最小的区间范围

本篇解法参考了[labuladong的C++滑窗模板](https://labuladong.gitbook.io/algo/di-yi-zhang-shou-ba-shou-shua-shu-ju-jie-gou/shou-ba-shou-shua-shu-zu-ti-mu/hua-dong-chuang-kou-ji-qiao-jin-jie)，并根据C代码实现做了部分调整，相关改变见注释。

## 实现代码
----
```c
#define MAX_LEN 128
char* minWindow(char * s, char * t)
{
    int hashNeed[MAX_LEN] = {0};
    int hashWindow[MAX_LEN] = {0};
    int start = 0;
    int end = 0;
    int minStart = 0;
    int minLen = INT_MAX;
    int lenS = strlen(s);
    int i, lenT = 0;
    int valid = 0;

    for (i = 0; t[i]; i++) {
        hashNeed[t[i]]++;
    }
    // lenT = i;  // 原表达长度
    for (i = 0; i < MAX_LEN; i++) {
        if (hashNeed[i] != 0) { lenT++; } // 改成表达键元素个数
    }

    // 记录最小覆盖子串的起始索引及长度
    while (end < lenS) {
        // c 是将移入窗口的字符
        char c = s[end];
        // 右移窗口
        end++;
        // 进行窗口内数据的一系列更新
        // if (need.count(c)) { // 原C++代码，count函数是查找该值是否存在
        if (hashNeed[c]) { 		// 只用判hash值是否为0
            hashWindow[c]++;
            if (hashWindow[c] == hashNeed[c])
                valid++;
        }

        // 判断左侧窗口是否要收缩
        // while (valid == need.size()) { // 原代码表示的键个数， 如 aa, 则键只有1个，值为2
        while (valid == lenT) {           // 而lenT则为 aa的长度，为2
            // 在这里更新最小覆盖子串
            if (end - start < minLen) {
                minStart = start;
                minLen = end - start;
            }
            // d 是将移出窗口的字符
            char d = s[start];
            // 左移窗口
            start++;
            // 进行窗口内数据的一系列更新
            // if (need.count(d)) { // 原C++代码
            if (hashNeed[d]) {
                if (hashWindow[d] == hashNeed[d])
                    valid--;
                hashWindow[d]--;
            }
        }
    }
    // 返回最小覆盖子串
    if (minLen == INT_MAX) { return ""; }
    
    s[minStart + minLen] = '\0'; // 利用原字符串返回，省空间
    return &s[minStart];
}
```

## 更多滑窗题目
-----

题目链接：[LeetCode 76.最小覆盖子串（困难）](https://leetcode-cn.com/problems/minimum-window-substring/)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210426071745157.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE3MjU2Njg5,size_16,color_FFFFFF,t_70)

**Git项目地址**：[LeetCodeUsingC刷题笔记](https://github.com/ioMayday/leetcode_sol)