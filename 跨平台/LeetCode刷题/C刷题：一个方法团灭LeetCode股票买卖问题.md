
# C刷题：一个方法团灭LeetCode股票买卖问题
**目录**
[toc]

> **5.28更新：**
> 看到文章 [关于 labuladong抄的算法题解 这本书的讨论](https://leetcode-cn.com/circle/discuss/MKwnEl/)，大致结论可以得出，该书大部分内容确实是labuladong小抄，并非其原创，希望大家加以分辨了解，并支持真正的原创者。

**Git项目地址**：[LeetCodeUsingC刷题笔记](https://github.com/ioMayday/leetcode_sol)

本篇解法思路参考了[labuladong的算法小抄](https://labuladong.gitbook.io/algo/)里的文章《一个方法团灭 LeetCode 股票买卖问题》，将原来C++代码，根据C代码实现做了部分调整和简化。总体来看，东哥文章里面思路讲的已经很清楚了，下面总结下C代码实现对应框架时，个人认为的一些核心要点和注意事项。

## 核心框架
----

**关键点**

- 理解状态遍历的思路本质，类似于知道初始条件，不断迭代所有可能，最终得到结果
- 记住状态转移方程写法和初始条件，并灵活应用
- 买前必须卖完
- 买完再卖出去后才算一笔交易

若假设一共有`n`天股价数据，最大交易次数限制为`K`次，则`0 <= i < n`和`0 <= k <= K`，可以推出：

**状态转移方程：**

```c
dp[i][k][0] = max(dp[i - 1][k][0], dp[i - 1][k][1] + price[i]);
dp[i][k][1] = max(dp[i - 1][k][1], dp[i - 1][k - 1][0] - price[i]);
```
解释：
- 简化问题，每天都只有两个状态，持有和不持有，当前状态跟前一个状态有关系，于是就可以从初始条件不断迭代，直到最后一天。
- 另外，之所以每个是用`max`取结果，是因为**dp存的一直是到今天截止当前状态对应的最大利润**。所以我们的输出才可以是dp[n - 1][K][0]。


**初始条件Base Case:**
```c
dp[-1][k][0] = 0;			// 注意此处的k表示0到k的操作次数所有值	
dp[-1][k][1] = -Infinity; 	// 表示不可能
dp[i][0][0] = 0;			// 注意此处的i表示0到i-1的天数所有值
dp[i][0][1] = -Infinity;
```

**输出的最大利润：**

```c
dp[n - 1][k][0]  			// 最后一天，手上不持有股票的时候有最大利润
```
以上为核心框架分析，下面开始实战。
## 5道题中的易错点

---
### 买卖股票的最佳时机
`K = 1`，即仅允许交易一次的情况，因为我们最终只关心`dp[n - 1][K][0]`时的输出，所以状态转移方程中`k`可以直接取1，而`dp[i - 1][0][0]`恒为0，所以可以用`k = 1`一直递推，从而降维得到：
```c
dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] + prices[i])
dp[i][1] = max(dp[i - 1][1], -prices[i])
```

### 买卖股票的最佳时机 II
`K = +infinity`，即不限交易次数的情况，每天的dp里显然只需要存储最大交易次数对应的利润即可。而我们只关心最终`dp[n - 1][K][0]`的输出，且K无穷大时，`dp[i - 1][K - 1][0] = dp[i - 1][K][0]`，所以状态转移方程可以化简为：

```c
dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] + prices[i])
dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] - prices[i])
```

### 买卖股票的最佳时机 III
`K = 2`，即仅能交易两次，注意以下两点：

 - 不能只用两个变量来表达，要用两个大小为3的一维数组，表示k从0一直取到2 
 - 要注意数组初始值，不持有状态初始值全为0，持有状态初始全为负无穷大

### 最佳买卖股票时机含冷冻期（中等）
容易想到，冷冻期一天的限制条件可转化为：一旦发现卖出，则在下一天循环时跳过。以下为对应代码实现：
```c
#include <limits.h>

// 采用状态机来遍历所有可能的状态
#define max(a, b) (a) > (b) ? (a) : (b)
int maxProfit(int* prices, int pricesSize)
{
    // k = +infinity
    int dp_i_0 = 0;
    int dp_i_1 = INT_MIN;
    int freeze_flag = 0;
    int i;
    for (i = 0; i < pricesSize; i++) {
        // dp[i][k][0] = max(dp[i - 1][k][0], dp[i - 1][k][1] + price[i]); // 不动或卖出
        // dp[i][k][1] = max(dp[i - 1][k][1], dp[i - 1][k - 1][0] - price[i]); // 不动或买入
        if (freeze_flag == 1) {
            freeze_flag = 0;
            continue; // 卖出后冻结一天
        }
        int tmp_dp_i_0 = dp_i_0;
        // dp_i_0 = max(dp_i_0, dp_i_1 + prices[i]);
        if (dp_i_1 + prices[i] > dp_i_0) { // 若卖出
            dp_i_0 = dp_i_1 + prices[i];
            freeze_flag = 1;
        }
        dp_i_1 = max(dp_i_1, tmp_dp_i_0 - prices[i]);

    }
    return dp_i_0;
}
```
但是以上代码在过用例`{1,2,4}`时，输出错误。分析发现，状态转移方程更新的不对，状态转移方程中这句：
`dp[i][k][1] = max(dp[i - 1][k][1], dp[i - 1][k - 1][0] - price[i])`
应该是`dp[i - 2][k - 1][0]`。修改上面**19-25行**代码为：

```c
int tmp_dp_i_0 = dp_i_0;
// dp_i_0 = max(dp_i_0, dp_i_1 + prices[i]);
if (dp_i_1 + prices[i] > dp_i_0) { // 若卖出
    dp_i_0 = dp_i_1 + prices[i];
    freeze_flag = 1;
}
dp_i_1 = max(dp_i_1, pre_dp_i_0 - prices[i]);
pre_dp_i_0 = tmp_dp_i_0;
```
再测试时，依然输出结果不对。深入分析后，发现前面说的转化思路不对，`continue`会导致无法迭代每一步转移矩阵，从而无法得到所有可能情况。

比如输入{1,2,4}时，在不持有的可能中，第二天就卖了，检测到卖了标志，第三天就冻结了。而实际最优的结果不是取的第二天就卖，而是取的第二天持有，然后第三天卖这样的操作，才能获得利润最大化。所以代码根据其中的一种卖出可能，就当做真的已经卖出跳过其他可能的遍历了。

**正确做法如下：**
- 修改状态转移方程，买入时，从前两天不持有状态的数组转移过来，`i-1` 变为 `i-2`
- 不要跳过，而是遍历所有天数的可能

正确代码如下：

```c
#include <limits.h>
#define max(a, b) (a) > (b) ? (a) : (b)
int maxProfit(int* prices, int pricesSize)
{
    // k = +infinity
    int dp_i_0 = 0;
    int dp_i_1 = INT_MIN;
    int i;
    int pre_dp_i_0 = 0;
    for (i = 0; i < pricesSize; i++) {
        // dp[i][k][0] = max(dp[i - 1][k][0], dp[i - 1][k][1] + price[i]); // 不动或卖出
        // dp[i][k][1] = max(dp[i - 1][k][1], dp[i - 2][k - 1][0] - price[i]); // 不动或买入
        int tmp = dp_i_0; // 更新前
        dp_i_0 = max(dp_i_0, dp_i_1 + prices[i]); // 当前更新后
        dp_i_1 = max(dp_i_1, pre_dp_i_0 - prices[i]);
        pre_dp_i_0 = tmp; // 下次循环用时，就变成了前两天的
    }
    return dp_i_0;
}
```
### 买卖股票的最佳时机含手续费
此题要注意初始条件会从INT_MIN再减个手续费，可能导致整数类型溢出。需要注意防止超过int最小值, 如 `INT_MIN + 1 - 2` 就溢出了。

解决的小技巧：

```c
// 原来
dp_i_0 = max(dp_i_0, dp_i_1 + prices[i] - fee);
// 改为
int tmp = ((long long)dp_i_1 + prices[i] - fee < INT_MIN) ? 
           INT_MIN : (dp_i_1 + prices[i] - fee);
dp_i_0 = max(dp_i_0, tmp);
```


完整代码如下：
```c
// 采用状态机来遍历
/**** 模板 **********
状态转移方程：
dp[i][k][0] = max(dp[i - 1][k][0], dp[i - 1][k][1] + price[i]);
dp[i][k][1] = max(dp[i - 1][k][1], dp[i - 1][k - 1][0] - price[i]);

Base Case:
dp[-1][k][0] = 0;
dp[-1][k][1] = -Infinity; //不可能
dp[i][0][0] = 0;
dp[i][0][1] = -Infinity;

output: 
dp[n - 1][k][0]
********************/
#include <limits.h>
#define max(a, b) (a) > (b) ? (a) : (b)
int maxProfit(int* prices, int pricesSize, int fee)
{
    // k = +infinity
    int dp_i_0 = 0;
    int dp_i_1 = INT_MIN;
    int i;
    for (i = 0; i < pricesSize; i++) {
        // dp[i][k][0] = max(dp[i - 1][k][0], dp[i - 1][k][1] + price[i] - fee); // 卖出时才扣手续费
        // dp[i][k][1] = max(dp[i - 1][k][1], dp[i - 1][k - 1][0] - price[i]);
        int tmp_dp_i_0 = dp_i_0;
        // 注意防止超过int最小值, 如 INT_MIN + 1 - 2 就溢出了
        int tmp = ((long long)dp_i_1 + prices[i] - fee < INT_MIN) ? INT_MIN : (dp_i_1 + prices[i] - fee);
        dp_i_0 = max(dp_i_0, tmp);
        dp_i_1 = max(dp_i_1, tmp_dp_i_0 - prices[i]);
    }
    return max(dp_i_0, dp_i_1);
}
```

## LeetCode相关题目

------

 - [买卖股票的最佳时机（简单）](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/solution/) 
 - [买卖股票的最佳时机 II（简单）](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/) 
 - [买卖股票的最佳时机 III（困难）](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii/) 
 - [买卖股票的最佳时机 IV（困难）](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv/)
 -  [最佳买卖股票时机含冷冻期（中等）](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/) 
 - [买卖股票的最佳时机含手续费（中等）](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)

**完整C代码实现**，可去GitHub仓：[LeetCodeUsingC刷题笔记](https://github.com/ioMayday/leetcode_sol)查看。







