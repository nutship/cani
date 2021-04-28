<!-- prettier-ignore-start -->

> 给定一个整数数组 `nums` ，找到其中最长严格递增子序列的长度。<br>
> Example1:
```
入：nums = [10,9,2,5,3,7,101,18]
输出：4
解释：最长递增子序列是 [2,3,7,101]，因此长度为 4 。
```
> Example2
```
输入：nums = [7,7,7,7,7,7,7]
输出：1
```
Note:
>
-   1 <= nums.length <= 2500
-   -10^4 <= nums[i] <= 10^4

<!-- prettier-ignore-end -->

### 简单 DP

令 $dp[i]$ 表示以 $\mathrm{nums}[i]$ 结尾的 LIS 长度，则:

$$
dp[i] = \max_{\substack{0 \leq j \leq i-1\\\\ \mathrm{nums}[j] < \mathrm{nums}[i]}} \lbrace \ dp[j] + 1 \ \rbrace
$$

??? adcodes "solution"

    ```java
    public int lengthOfLIS(int[] nums) {
        int[] dp = new int[nums.length];
        int maxLen = 1;
        dp[0] = 1;
        for (int i = 1; i < nums.length; ++i) {
            dp[i] = 1;
            for (int j = i - 1; j >= 0; --j) {
                if (nums[j] < nums[i])
                    dp[i] = Math.max(dp[i], dp[j] + 1);
            }
            maxLen = Math.max(dp[i], maxLen);
        }
        return maxLen;
    }
    ```

时间 $O(n^2)$，空间 $O(n)$

### DP + 二分

构造 LIS 的过程中，对于等长的 LIS，应使 `LIS[-1]` 尽可能小。受此启发，令 $dp_i[k]$ 表示 $\mathrm{nums}[0...i]$ 中长为 $k+1$ 的 `LIS[-1]` 的最小值

$$
dp_i[k] =
\begin{cases}
\mathrm{nums}[i] & \text{if } dp_{i-1}[k-1] < \text{nums}[i]\leq dp_{i-1}[k] \\\\
dp_{i-1}[k] &\text{else}
\end{cases}
$$

实际只需维护一个一维数组，由于 $dp_{i-1}[0...\mathsf{maxLen})$ 部分一定是有序的，可以通过二分查找，找到第一个大于或等于 $\mathrm{nums}[i]$ 的元素; 如果查找失败，说明 $\text{nums}[i]$ 很大，需要扩展 $\mathsf{maxLen}$

??? adcodes "solution"

    ```java
    public int lengthOfLIS(int[] nums) {
        int[] dp = new int[nums.length];
        dp[0] = nums[0];
        int maxLen = 1;
        for (int i = 1; i < nums.length; ++i) {
            int l = 0, r = maxLen;
            while (l < r) {
                int mid = (l + r) / 2;
                if (nums[i] > dp[mid])
                    l = mid + 1;
                else if (nums[i] < dp[mid])
                    r = mid;
                else
                    l = r = mid;
            }
            dp[l] = nums[i];
            maxLen = Math.max(l + 1, maxLen);
        }
        return maxLen;
    }
    ```

时间 $O(n\log n)$，空间 $O(n)$
