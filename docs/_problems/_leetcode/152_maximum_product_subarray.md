<!-- prettier-ignore-start -->

> 给定一个整数数组 `nums` ，请你找出数组中乘积最大的连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。<br>
> Example1:
```
输入: [2,3,-2,4]
输出: 6
解释: 子数组 [2,3] 有最大乘积 6。
```
> Example2
```
输入: [-2,0,-1]
输出: 0
解释: 结果不能为 2, 因为 [-2,-1] 不是子数组。
```
Note:
>
-   1 <= nums.length <= 2 \* 10^4
-   -10 <= nums[i] <= 10
-   The product of any prefix or suffix of nums is guaranteed to fit in a 32-bit integer

<!-- prettier-ignore-end -->

<br>

令 $dp_{max}[i]$ 以 $\mathrm{nums}[i]$ 结尾的最大乘积子数组，$dp_{min}[i]$ 同理，则:

$$
\begin{aligned}
&\mathsf{if}\ \mathrm{nums}[i] \geq 0 \\\\
&\qquad dp_{max}[i] = \max\lbrace dp_{max}[i-1] \times \mathrm{nums}[i],\enspace \mathrm{nums}[i] \rbrace \\\\
&\qquad dp_{min}[i] = \min\lbrace dp_{min}[i-1] \times \mathrm{nums}[i],\enspace \mathrm{nums}[i] \rbrace \\\\
&\mathsf{else} \\\\
&\qquad dp_{max}[i] = \max\lbrace dp_{min}[i-1] \times \mathrm{nums}[i],\enspace \mathrm{nums}[i] \rbrace \\\\
&\qquad dp_{min}[i] = \min\lbrace dp_{max}[i-1] \times \mathrm{nums}[i],\enspace \mathrm{nums}[i] \rbrace \\\\
\end{aligned}
$$

??? adcodes "solution"

    ```java
    public int maxProduct(int[] nums) {
        int maxPro = nums[0], minPro = nums[0];
        int m = maxPro;
        for (int i = 1; i < nums.length; ++i) {
            if (nums[i] >= 0) {
                maxPro = Math.max(maxPro * nums[i], nums[i]);
                minPro = Math.min(minPro * nums[i], nums[i]);
            }
            else {
                int tMaxPro = Math.max(minPro * nums[i], nums[i]);
                minPro = Math.min(maxPro * nums[i], nums[i]);
                maxPro = tMaxPro;
            }
            m = Math.max(m, maxPro);
        }
        return m;
    }
    ```
