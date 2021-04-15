<!-- prettier-ignore-start -->
> 给定一个只包含正整数的非空数组。是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。<br><br>
> Example:
```
输入: [1, 5, 11, 5]
输出: true
解释: 数组可以分割成 [1, 5, 5] 和 [11].
```
Note:
>
-   1 <= nums.length <= 200
-   1 <= nums[i] <= 100

<!-- prettier-ignore-end -->

<br>

01 背包，判断方案是否存在即可，求方案数会导致溢出

??? adcodes "partition equal subset sum"

    ```java
    public boolean canPartition(int[] nums) {
        int sum = 0;
        for (int n : nums)
            sum += n;
        if (sum % 2 == 1)
            return false;
        sum /= 2;
        boolean[] dp = new boolean[sum + 1];
        dp[0] = true;
        if (nums[0] <= sum)
            dp[nums[0]] = true;
        for (int i = 1; i < nums.length; ++i) {
            for (int c = sum; c >= 0; --c) {
                if (c - nums[i] >= 0)
                    dp[c] |= dp[c - nums[i]];
            }
        }

        return dp[sum];
    }
    ```
