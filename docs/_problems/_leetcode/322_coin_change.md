<!-- prettier-ignore-start -->
> 给定不同面额的硬币 coins 和一个总金额 amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回  -1。<br><br>
> Example1:
```
输入：coins = [1, 2, 5], amount = 11
输出：3
解释：11 = 5 + 5 + 1
```
Example2:
```
输入：coins = [2], amount = 3
输出：-1
```
Note:
>
-   1 <= coins.length <= 12
-   1 <= coins[i] <= 2^31 - 1
-   0 <= amount <= 10^4

<!-- prettier-ignore-end -->

<br>

完全背包 + 恰好装满 + 最小价值

-   `dp[i][c] = min { dp[i - 1][c], dp[i][c - coins[i]] + 1 }`
-   `dp[0][0] = 0, dp[0][1...] = +INF`
-   `i` 从 0 开始计算， `dp` 数组正向遍历

??? adcodes "coin change"

    ```java
    public int coinChange(int[] coins, int amount) {
        int[] dp = new int[amount + 1];
        for (int i = 1; i < amount + 1; ++i)
            dp[i] = 99999;
        for (int i = 0; i < coins.length; ++i) {
            for (int c = 0; c < amount + 1; ++c) {
                if (c - coins[i] >= 0)
                    dp[c] = min(dp[c], dp[c - coins[i]] + 1);
            }
        }
        return dp[amount] >= 99999 ? -1 : dp[amount];
    }
    ```
