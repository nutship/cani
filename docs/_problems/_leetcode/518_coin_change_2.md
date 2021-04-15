<!-- prettier-ignore-start -->
> 给定不同面额的硬币和一个总金额。写出函数来计算可以凑成总金额的硬币组合数。假设每一种面额的硬币有无限个。<br><br>
Example1: 
```
输入: amount = 5, coins = [1, 2, 5]
输出: 4
```
Example2:
```
输入: amount = 3, coins = [2]
输出: 0
```
Note:
>
- 0 <= amount <= 5000, 1 <= coin <= 5000
- 硬币种类 <= 500, 结果符合有符号 int

<!-- prettier-ignore-end -->

<br>

完全背包 + 求方案数 + 恰好装满:

-   `dp[i][c] = dp[i-1][c] + dp[i][c - coins[i]]`
-   `dp[0][0] = 1`, `dp[0][1...] = 0`
-   `i` 从 0 开始计算，`dp` 数组正向遍历

??? adcodes "coin change 2"

    ```java
    public int change(int amount, int[] coins) {
        int[] dp = new int[amount + 1];
        dp[0] = 1;
        for (int i = 0; i < coins.length; ++i) {
            for (int c = 0; c < amount + 1; ++c) {
                if (c - coins[i] >= 0)
                    dp[c] += dp[c - coins[i]];
            }
        }
        return dp[amount];
    }
    ```
