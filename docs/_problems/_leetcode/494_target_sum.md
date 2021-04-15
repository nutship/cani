<!-- prettier-ignore-start -->
> 给定一个非负整数数组，a1, a2, ..., an, 和一个目标数，S。现在你有两个符号 + 和 -。对于数组中的任意一个整数，你都可以从 + 或 -中选择一个符号添加在前面。<br>
返回可以使最终数组和为目标数 S 的所有添加符号的方法数<br><br>
Example: 
```
输入：nums: [1, 1, 1, 1, 1], S: 3
输出：5
解释：

-1+1+1+1+1 = 3
+1-1+1+1+1 = 3
+1+1-1+1+1 = 3
+1+1+1-1+1 = 3
+1+1+1+1-1 = 3
```
Note:
>
- 数组非空，且长度不会超过 20 
- 初始的数组的和不会超过 1000 
- 保证返回的最终结果能被 32 位整数存下

<!-- prettier-ignore-end -->

<br>

类似于 01 背包，

-   数组和不会超过 1000，i.e. 等式取值在 [-1000, 1000] 范围内，由于取值作为数组索引必须为正，可以假设两侧同加 1000
-   用 `dp[i][c]` 表示 `[x0,...,xi]` 内和为 `c` 的方案数，则 <br>`dp[i][c] = dp[i - 1][c + nums[i]] + dp[i - 1][c - nums[i]]`
-   注意 `nums[0]` 可能为 0

??? adcodes "target sum"

    ```java
    public int findTargetSumWays(int[] nums, int target) {
        int MAX = 2001;
        int[] last = new int[MAX];
        int[] curr = new int[MAX];
        last[1000 + nums[0]] += 1;
        last[1000 - nums[0]] += 1;
        int[] tmp = last;

        for (int i = 1; i < nums.length; ++i) {
            int val = nums[i];
            for (int c = 0; c < last.length; ++c) {
                int last1 = (c + val > 2000) ? 0 : last[c + val];
                int last2 = (c - val < 0) ? 0 : last[c - val];
                curr[c] = last1 + last2;
            }
            tmp = curr;
            curr = last;
            last = tmp;
        }

        return tmp[1000 + target];
    }
    ```
