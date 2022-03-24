<!-- prettier-ignore-start -->
> 给你一个二进制字符串数组 strs 和两个整数 m 和 n 。请你找出并返回 strs 的最大子集的大小，该子集中 最多 有 m 个 0 和 n 个 1.<br>
如果 x 的所有元素也是 y 的元素，集合 x 是集合 y 的 子集<br><br>
> Example:
```
输入：strs = ["10", "0001", "111001", "1", "0"], m = 5, n = 3
输出：4
解释：最多有 5 个 0 和 3 个 1 的最大子集是 {"10","0001","1","0"} ，因此答案是 4 。
其他满足题意但较小的子集包括 {"0001","1"} 和 {"10","1","0"} 。
{"111001"} 不满足题意，因为它含 4 个 1 ，大于 n 的值 3 。
```
Note:
>
-   1 <= strs.length <= 600, &ensp; 1 <= strs[i].length <= 100
-   strs[i] 仅由 '0' 和 '1' 组成
-   1 <= m, n <= 100

<!-- prettier-ignore-end -->

<br>

二维 01 背包，`dp[i][C1][C2] = max{dp[i - 1][C1][C2], dp[i - 1][C1 - nZeros][C2 - nOnes] + 1}`

??? adcodes "ones and zeros"

    ```java
    public int findMaxForm(String[] strs, int m, int n) {
        int[][] dp = new int[m + 1][n + 1];
        int nZeros0 = getZerosNum(strs[0]);
        int nOnes0 = strs[0].length() - nZeros0 ;
        for (int i = 0; i < m + 1; ++i)
            for (int j = 0; j < n + 1; ++j)
                if (i >= nZeros0  && j >= nOnes0 )
                    dp[i][j] = 1;
        for (int l = 1; l < strs.length; ++l) {
            String str = strs[l];
            int nZeros = getZerosNum(str);
            int nOnes = str.length() - nZeros;
            for (int i = m; i >= 0; --i) {
                for (int j = n; j >= 0; --j) {
                    if (i - nZeros >= 0 && j - nOnes >= 0)
                        dp[i][j] = max(dp[i][j], dp[i - nZeros][j - nOnes] + 1);
                }
            }
        }

        return dp[m][n];
    }
    ```
