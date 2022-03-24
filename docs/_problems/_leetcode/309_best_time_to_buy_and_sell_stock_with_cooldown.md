<!-- prettier-ignore-start -->

> 给定一个数组 `prices` ，它的第 `i` 个元素 `prices[i]` 表示一支给定股票第 i 天的价格。<br>
> 设计一个算法来计算你所能获取的最大利润。你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票。你可以尽可能地完成更多的交易（多次买卖一支股票）<br>
> 卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天) <br>
> Example1:
```
输入: [1,2,3,0,2]
输出: 3 
解释: 对应的交易状态为: [买入, 卖出, 冷冻期, 买入, 卖出]
```
Note:
>
-   1 <= prices.length <= 5000
-   0 <= prices[i] <= 1000

<!-- prettier-ignore-end -->

<br>

相较于 <a href="../122_best_time_to_sell_stock_ii">122. best time to sell stock ii</a>，多了一个冷冻状态。沿用其表示 (考虑第 $i$ 天结束时)，并令冷冻状态为 2，输入 `[1,2,3,0,2]` 对应的状态序列为 `[1,2,0,1,2]`，因此有:

$$
dp_2[i] = dp_1[i-1] + prices[i] \\\\
dp_0[i] = \max\lbrace \ dp_0[i-1], \enspace dp_2[i-1] \ \rbrace = \max\lbrace \ dp_0[i-1], \enspace dp_1[i-2] + prices[i-1] \ \rbrace \\\\
dp_1[i] = \max\lbrace \ dp_1[i-1], \enspace dp_0[i-1] - prices[i] \ \rbrace
$$

$\max\lbrace\ dp_0[N-1],\enspace dp_2[N-1]\ \rbrace$ 即为最终结果

??? adcodes "solution"

    ```java
    public int maxProfit(int[] prices) {
        int N = prices.length;
        if (N == 1)
            return 0;
        int[] dp0 = new int[N], dp1 = new int[N];
        dp1[0] = -prices[0];
        dp1[1] = Math.max(-prices[0], -prices[1]);
        for (int i = 2; i < N; ++i) {
            dp0[i] = Math.max(dp0[i - 1], dp1[i - 2] + prices[i - 1]);
            dp1[i] = Math.max(dp1[i - 1], dp0[i - 1] - prices[i]);
        }
        return Math.max(dp0[N - 1], dp1[N - 2] + prices[N - 1]);
    }
    ```
