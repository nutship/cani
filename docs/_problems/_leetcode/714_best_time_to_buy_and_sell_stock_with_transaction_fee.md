<!-- prettier-ignore-start -->

> 给定一个数组 `prices` ，它的第 `i` 个元素 `prices[i]` 表示一支给定股票第 i 天的价格。非负整数 `fee` 代表了交易股票的手续费用。<br>
> 你可以无限次地完成交易，但是你每笔交易都需要付手续费。如果你已经购买了一个股票，在卖出它之前你就不能再继续购买股票了。<br>
> 返回获得利润的最大值。 <br>
> Example1:
```
输入: prices = [1, 3, 2, 8, 4, 9], fee = 2
输出: 8
解释: 能够达到的最大利润:  
在此处买入 prices[0] = 1
在此处卖出 prices[3] = 8
在此处买入 prices[4] = 4
在此处卖出 prices[5] = 9
总利润: ((8 - 1) - 2) + ((9 - 4) - 2) = 8.
```
Note:
>
-   0 < prices.length <= 50000
-   0 < prices[i] < 50000
-   0 <= fee < 50000

<!-- prettier-ignore-end -->

<br>

和 <a href="../122_best_time_to_sell_stock_ii">122. best time to sell stock ii</a> 基本一致，只是不能贪心

??? adcodes "solution"

    ```java
    public int maxProfit(int[] prices, int fee) {
        int N = prices.length;
        int dp0 = 0, dp1 = -prices[0];
        for (int i = 1; i < N; ++i) {
            dp0 = Math.max(dp0, dp1 + prices[i] - fee);
            dp1 = Math.max(dp1, dp0 - prices[i]);
        }
        return dp0;
    }
    ```
