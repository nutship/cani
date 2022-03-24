<!-- prettier-ignore-start -->

> 给定一个数组 `prices` ，它的第 `i` 个元素 `prices[i]` 表示一支给定股票第 i 天的价格。<br>
> 设计一个算法来计算你所能获取的最大利润。你最多可以完成 两笔 交易<br>
> 返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 0 <br>
> Example1:
```
输入：prices = [3,3,5,0,0,3,1,4]
输出：6
解释：在第 4 天（股票价格 = 0）的时候买入，在第 6 天（股票价格 = 3）的时候卖出，这笔交易所能获得利润 = 3-0 = 3 。
     随后，在第 7 天（股票价格 = 1）的时候买入，在第 8 天 （股票价格 = 4）的时候卖出，这笔交易所能获得利润 = 4-1 = 3 。

```
> Example2:
```
输入：prices = [1,2,3,4,5]
输出：4
解释：在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。   
     注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。   
     因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。
```
Note:
>
-   1 <= prices.length <= 10^5
-   0 <= prices[i] <= 10^4

<!-- prettier-ignore-end -->

### DP

由于最多只能两笔交易，在任意一天结束时，可能有 0、买、买卖、买卖买、买卖买卖 五种状态，令 $dp_{buy2}[i]$ 表示第 $i$ 天结束时的最大利润且当前已购买 2 次股票，其余同理:

$$
\begin{aligned}
dp_{buy1}[i] &= \max\lbrace\ dp_{buy1}[i-1],\enspace -prices[i]\ \rbrace \\\\
dp_{sell1}[i] &= \max\lbrace\ dp_{sell1}[i-1],\enspace dp_{buy1}[i-1]+prices[i]\ \rbrace \\\\
dp_{buy2}[i] &= \max\lbrace\ dp_{buy2}[i-1],\enspace dp_{sell1}[i-1]-prices[i]\ \rbrace \\\\
dp_{sell2}[i] &= \max\lbrace\ dp_{sell2}[i-1],\enspace dp_{buy2}[i-1]+prices[i]\ \rbrace
\end{aligned}
$$

如果允许同一天内多次购买或出售股票，并不影响原问题的结果，还简化了实现:

-   $dp_{buy1}[i]$ 比 $dp_{buy1}[i-1]$ 多考虑了第 $i$ 天买入股票的情况，令 $dp_{sell1}[i] = \max\lbrace\ dp_{sell1}[i-1],\enspace dp_{buy1}[i]+prices[i]\ \rbrace$ 也无妨
-   初始 $dp_{buy1}[0] = dp_{buy2}[0] = -prices[0],\enspace dp_{sell1}[0] = dp_{sell2}[0] = 0$

??? adcodes "solution"

    ```java
    public int maxProfit(int[] prices) {
        int buy1 = -prices[0], sell1 = 0, buy2 = -prices[0], sell2 = 0;
        for (int i = 1; i < prices.length; ++i) {
            buy1 = Math.max(buy1, -prices[i]);
            sell1 = Math.max(sell1, buy1 + prices[i]);
            buy2 = Math.max(buy2, sell1 - prices[i]);
            sell2 = Math.max(sell2, buy2 + prices[i]);
        }
        return sell2;
    }
    ```

### DP2

受 <a href="../122_best_time_to_sell_stock_ii">122. best time to sell stock ii</a> 的影响，第一反应是令 $dp_0[i][j]$ 表示第 $i$ 天结束时已完成 $j$ 笔交易的最大利润 且 第 $i$ 天不持有股票，同样允许一天内购买出售股票，则:

$$
\begin{aligned}
dp_0[i][j] &= \max\lbrace\ dp_0[i - 1][j], \enspace dp_1[i][j - 1] + prices[i]\ \rbrace \\\\
dp_1[i][j] &= \max\lbrace\ dp_1[i - 1][j], \enspace dp_0[i][j] - prices[i]\ \rbrace
\end{aligned}
$$

??? adcodes "solution"

    ```java
    public int maxProfit(int[] prices) {
        int N = prices.length;
        int[][] dp0 = new int[N][3];
        int[][] dp1 = new int[N][3];
        dp1[0][0] = -prices[0];
        for (int i = 1; i < N; ++i)
            dp1[i][0] = Math.max(dp1[i - 1][0], -prices[i]);
        for (int j = 1; j <= 2; ++j) {
            dp1[0][j] = -prices[0];
            for (int i = 1; i < N; ++i) {
                dp0[i][j] = Math.max(dp0[i - 1][j], dp1[i][j - 1] + prices[i]);
                dp1[i][j] = Math.max(dp1[i - 1][j], dp0[i][j] - prices[i]);
            }
        }

        return dp0[N - 1][2];
    }
    ```
