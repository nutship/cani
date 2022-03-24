<!-- prettier-ignore-start -->

> 给定一个数组 `prices` ，它的第 `i` 个元素 `prices[i]` 表示一支给定股票第 i 天的价格。<br>
> 设计一个算法来计算你所能获取的最大利润。你最多可以完成 `k` 笔 交易<br>
> 注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）<br>
> Example1:
```
输入：k = 2, prices = [2,4,1]
输出：2
解释：在第 1 天 (股票价格 = 2) 的时候买入，在第 2 天 (股票价格 = 4) 的时候卖出，这笔交易所能获得利润 = 4-2 = 2 。
```
> Example2:
```
输入：k = 2, prices = [3,2,6,5,0,3]
输出：7
解释：在第 2 天 (股票价格 = 2) 的时候买入，在第 3 天 (股票价格 = 6) 的时候卖出, 这笔交易所能获得利润 = 6-2 = 4 。
     随后，在第 5 天 (股票价格 = 0) 的时候买入，在第 6 天 (股票价格 = 3) 的时候卖出, 这笔交易所能获得利润 = 3-0 = 3 。
```
Note:
>
-   0 <= k <= 100
-   0 <= prices.length <= 1000
-   0 <= prices[i] <= 1000

<!-- prettier-ignore-end -->

<br>

已用此法做过 <a href="../123_best_time_to_sell_stock_iii">123. best time to sell stock iii</a>，不同在于本题可以有 `prices.length = 0`

$$
\begin{aligned}
dp_0[i][j] &= \max\lbrace\ dp_0[i - 1][j], \enspace dp_1[i][j - 1] + prices[i]\ \rbrace \\\\
dp_1[i][j] &= \max\lbrace\ dp_1[i - 1][j], \enspace dp_0[i][j] - prices[i]\ \rbrace
\end{aligned}
$$

??? adcodes "solution"

    ```java
    public int maxProfit(int k, int[] prices) {
        int N = prices.length;
        if (N == 0)
            return 0;
        int[][] dp0 = new int[N][k + 1];
        int[][] dp1 = new int[N][k + 1];
        dp1[0][0] = -prices[0];
        for (int i = 1; i < N; ++i)
            dp1[i][0] = Math.max(dp1[i - 1][0], -prices[i]);
        for (int j = 1; j <= k; ++j) {
            dp1[0][j] = -prices[0];
            for (int i = 1; i < N; ++i) {
                dp0[i][j] = Math.max(dp0[i - 1][j], dp1[i][j - 1] + prices[i]);
                dp1[i][j] = Math.max(dp1[i - 1][j], dp0[i][j] - prices[i]);
            }
        }

        return dp0[N - 1][k];
    }
    ```
