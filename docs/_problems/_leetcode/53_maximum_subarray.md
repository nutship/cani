<!-- prettier-ignore-start -->

> 给定一个整数数组 `nums` ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。<br>
> Example1:
```
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。
```
Note:
>
-   1 <= nums.length <= 3 * 10^4
-   -10^5 <= nums[i] <= 10^5

<!-- prettier-ignore-end -->

### DP

令 $dp[i]$ 表示以 $\mathrm{nums}[i]$ 结尾的最大子数组的和，则:

$$
dp[i] = \max\lbrace \ dp[i-1]+\mathrm{nums}[i],\enspace \mathrm{nums}[i]  \ \rbrace
$$

??? adcodes "solution"

    ```java
    public int maxSubArray(int[] nums) {
        int sum = nums[0];
        int maxSum = sum;
        for (int i = 1; i < nums.length; ++i) {
            sum = Math.max(sum + nums[i], nums[i]);
            maxSum = Math.max(sum, maxSum);
        }
        return maxSum;
    }
    ```
