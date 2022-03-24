<!-- prettier-ignore-start -->

> 给定字符串 `s` 和一个字符规律 `p`，实现一个支持 `'.'` 和 `'*'` 的正则表达式匹配 <br>
> Example1:
```
输入：s = "aab" p = "c*a*b"
输出：true
```
> Example2:
```
输入：s = "mississippi" p = "mis*is*p*."
输出：false
```
Note:
>
-   0 <= s.length <= 20, &ensp; 0 <= p.length <= 30
-   s 可能为空，且只包含从 a-z 的小写字母
-   p 可能为空，且只包含从 a-z 的小写字母，以及字符 . 和 *
-   保证每次出现字符 * 时，前面都匹配到有效的字符

<!-- prettier-ignore-end -->

<br>

$dp[i][j]$ 表示 $s[0...i)$ 和 $p[0...j)$ 是否匹配，这样设计方便空串表示

$ \displaystyle
\normalsize
\ \ dp[i][j]=
\begin{cases}
dp[i-1][j-1] \mathrm{\ and \ } s[i-1] = p[j-1] & \mathrm{if\ } p[j] \ne \mathsf{\*} \\\\
(dp[i-1][j-2] \mathrm{\ or\ } dp[i-1][j]) \mathrm{\ and \ } (s[i-1] = p[j-2]) \mathrm{\ or\ } dp[i][j-2] & \mathrm{if\ } p[j] = \mathsf{\*}
\end{cases}
$

需要注意下初始化时，$s$为空 匹配 $p$ 的 $\mathsf{\*}$ 的情形:

$\displaystyle
\ \ dp[ 0][0] = \mathrm{true},\enspace dp[ 1... ][0] = \mathrm{false},\enspace dp[0][j] = (p[j]=\mathsf{\*})\mathrm{\ and\ } (dp[0][j-2])
$

??? adcodes "re match"

    ```java
    public boolean isMatch(String s, String p) {
        int M = s.length(), N = p.length();
        boolean[][] dp = new boolean[M + 1][N + 1];
        dp[0][0] = true;
        for (int j = 1; j < N + 1; ++j)
            dp[0][j] = p.charAt(j - 1) == '*' && dp[0][j - 2];
        for (int i = 1; i < M + 1; ++i) {
            for (int j = 1; j < N + 1; ++j) {
                if (p.charAt(j - 1) != '*')
                    dp[i][j] = dp[i - 1][j - 1] && (p.charAt(j - 1) == s.charAt(i - 1) || p.charAt(j - 1) == '.');
                else
                    dp[i][j] = ((dp[i - 1][j - 2] || dp[i - 1][j])
                            && (p.charAt(j - 2) == s.charAt(i - 1) || p.charAt(j - 2) == '.')) || dp[i][j - 2];
            }
        }

        return dp[M][N];
    }
    ```

当 $p[j]=\mathsf{\*}$ 且 $s[i-1] \ne p[j-2]$ 时，可以只计算 $dp[i][j-2]$，因此可以尝试记忆化搜索 + 剪枝

??? adcodes "re match"

    ```java
    public boolean isMatch(String s, String p) {
        int M = s.length(), N = p.length();
        int[][] dp = new int[M + 1][N + 1]; // 0: 未找到, 1: false, 2: true
        return search(M, N, dp, s.toCharArray(), p.toCharArray()) == 2;
    }

    public int search(int i, int j, int[][] dp, char[] s, char[] p) {
        if (dp[i][j] != 0)
            return dp[i][j];
        if (j == 0)
            return (i == 0) ? 2 : 1;
        if (i == 0)
            return (p[j - 1] == '*' && search(i, j - 2, dp, s, p) == 2) ? 2 : 1;

        if (s[i - 1] == p[j - 1] || p[j - 1] == '.') {
            dp[i][j] = search(i - 1, j - 1, dp, s, p);
        }
        if (p[j - 1] == '*') {
            if (s[i - 1] == p[j - 2] || p[j - 2] == '.') {
                dp[i][j] = (search(i - 1, j - 2, dp, s, p) == 2 || search(i - 1, j, dp, s, p) == 2) ? 2 : 1;
            }
            dp[i][j] = (search(i, j - 2, dp, s, p) == 2) ? 2 : dp[i][j];
        }
        return dp[i][j];
    }
    ```
