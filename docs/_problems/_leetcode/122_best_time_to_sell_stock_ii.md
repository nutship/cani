<!-- prettier-ignore-start -->

> 给定一个数组 `prices` ，它的第 `i` 个元素 `prices[i]` 表示一支给定股票第 i 天的价格。<br>
> 设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。<br>
> 注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。<br>
> Example1:
```
输入: prices = [7,1,5,3,6,4]
输出: 7
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
     随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。
```
> Example2:
```
输入: prices = [1,2,3,4,5]
输出: 4
解释: 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
     注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。
```
Note:
>
-   1 <= prices.length <= 10^5
-   0 <= prices[i] <= 10^4

<!-- prettier-ignore-end -->

### DP

令 $dp_0[i]$ 表示第 $i$ 天结束时能获取的最大利润且第 $i$ 天结束时未持有股票，$dp_1[i]$ 表示结束时持有，则:

$$
\tag{1} dp_0[i] = \max\lbrace\ dp_0[i-1],\enspace dp_1[i-1] + prices[i] \ \rbrace
$$

$$
\tag{2} dp_1[i] = \max\lbrace\ dp_1[i-1],\enspace dp_0[i-1] - prices[i] \ \rbrace
$$

如果允许同一天内购买或出售股票，不影响原问题解的值。由于 $dp_0[i]$ 比 $dp_0[i-1]$ 多考虑了第 $i$ 天出售了股票的情况，令 $dp_1[i] = \max\lbrace dp_1[i-1], \ dp_0[i] - prices[i]\rbrace$ 也无妨，把 $(2)$ 代入 $(1)$ 也可以验证这个事实

??? adcodes "Solution"

    ```java
    public int maxProfit(int[] prices) {
        int N = prices.length;
        int dp0 = 0, dp1 = -prices[0];
        for (int i = 1; i < N; ++i) {
            dp0 = Math.max(dp0, dp1 + prices[i]);
            dp1 = Math.max(dp1, dp0 - prices[i]);
        }
        return dp0;
    }
    ```

### 贪心

该问题相当于求解一系列区间 $[x_i,x_{i+1},...,y_i]$，使得 $\sum y_i-x_i$ 最大，其贪心选择性可描述为：

> <ktb></ktb>
> 设序列为 $S=[x_i,...,x_j]$，设 $s=[a_i,a_{i+1},...,b_i]$ 是 $S$ 从左往右数第一个完全递增的区间 ($b_{i+1}< b_i$, $a_{i}< a_{i-1}< a_{i-2}...$)，则 $s$ 一定在最优解中

因此问题可转化为统计相邻递增整数的差

??? adcodes "solution"

    ```java
    public int maxProfit(int[] prices) {
        int profits = 0;
        for (int i = 1; i < prices.length; ++i) {
            if (prices[i] > prices[i - 1])
                profits += prices[i] - prices[i - 1];
        }
        return profits;
    }
    ```
