<!-- prettier-ignore-start -->

> 给定一个字符串 `s` 和一个字符模式 `p` ，实现一个支持 `?` 和 `*` 的通配符匹配:
>
- `?` 可以匹配任何单个字符
- `*` 可以匹配任意字符串（包括空字符串
>
> Example1:
```
输入:
s = "aa"
p = "*"
输出: true
```
> Example2:
```
s = "adceb"
p = "*a*b"
输出: true
```
Note:
>
-   s 可能为空，且只包含从 a-z 的小写字母
-   p 可能为空，且只包含从 a-z 的小写字母，以及字符 ? 和 *

<!-- prettier-ignore-end -->

<br>

### 1. dp

相当于 <a href="../10_regular_exp_matching" style="color:blue">10. 正则匹配</a> 的简化版

$$
dp[i][j] =
\begin{cases}
dp[i-1][j-1] \ \mathrm{\ and\ } \ (s[i-1] \mathrm{\ equals\ } p[j-1]) & \mathrm{if}\ p[j-1] \ne \* \\\\
dp[i][j-1] \ \mathrm{\ or\ } \ dp[i-1][j] \ \mathrm{\ or\ } \ dp[i-1][j-1] & \mathrm{else}
\end{cases}
$$

??? adcodes "wildcard matching"

    ```java
    public boolean isMatch(String s, String p) {
        int M = s.length(), N = p.length();
        boolean[][] dp = new boolean[M + 1][N + 1];
        dp[0][0] = true;
        for (int j = 1; j < N + 1; ++j)
            dp[0][j] = dp[0][j - 1] && p.charAt(j - 1) == '*';
        for (int i = 1; i < M + 1; ++i) {
            for (int j = 1; j < N + 1; ++j) {
                if (p.charAt(j - 1) != '*')
                    dp[i][j] = dp[i - 1][j - 1] && (s.charAt(i - 1) == p.charAt(j - 1) || p.charAt(j - 1) == '?');
                else
                    dp[i][j] = dp[i][j - 1] || dp[i - 1][j] || dp[i - 1][j - 1];
            }
        }
        return dp[M][N];
    }
    ```

<br>

### 2. 贪心

按照 $\*$ 把 $p$ split 成多个子串，考虑某个子串位于两个 $\*$ 之间的情况

$$
\begin{aligned}
p' = p[i-1:] &=\enspace \*\ p[i:j)\ \*\ p[j+1:k)\ \*\ ... \\\\
s' = s[m:] &=\enspace s[m]\ s[m+1]\ ... s[k]\ ...\ s[k+j-i]\ ...
\end{aligned}
$$

简单证明该情况下的贪心选择性:

> <ktb></ktb>
> 假设 $p[i:j)$ matches $s[k:k+j-i)$ 是正向搜索 $s[m:]$ 时的第一个满足条件的匹配，如果 $s'$ 匹配 $p'$ 的话，则存在一种匹配方案，使得 $\*\ p[i:j)$ matches $s[m:k+j-i)$
>
> > 假设没有匹配方案允许 $\*\ p[i:j)$ matches $s[m:k+j-i)$，而有方案允许 $p[i:j)$ matches $s[k':k'+j-i)$，其中 $k'\geq k+1$，这说明 $\*\ p[j+1:]$ matches $s[k'+j-1:]$ 成立，那么 $\*\ p[j+1:]$ matches $s[k+j-1:]$ 也成立，该方案也允许 $\*\ p[i:j)$ matches $s[m:k+j-i)$

位于 $p$ 首尾的子串比较特殊，也必须贴着 $s$ 的首尾匹配，因此可以在 $s$ 和 $p$ 的首尾添加相同的特殊字符，既保证这点，也保证 $p$ 不会以 $\*$ 开头或结尾。如果有多个 $\*$ 连续排列，按照一个 $\*$ 处理即可

??? adcodes "wildcard matching"

    ```java
    public boolean isMatch(String s, String p) {
        s = "S" + s + "E";
        p = "S" + p + "E";
        String[] subPats = p.split("\\*");
        int sPtr = 0, j = 0;
        while (j < subPats.length) {
            if (p.length() == 0) { // multiple '*'
                j += 1;
                continue;
            }
            boolean lose = true;
            String subPat = subPats[j];
            for (int i = sPtr; i <= s.length() - subPat.length(); ++i) {
                if (naiveMatch(s, subPat, i)) {
                    sPtr = i + subPat.length();
                    j += 1;
                    lose = false;
                    break;
                }
            }
            if (lose)
                return false;
        }
        return sPtr == s.length();
    }

    public boolean naiveMatch(String s, String p, int si) {
        for (int j = 0; j < p.length(); ++j, ++si)
            if (p.charAt(j) != s.charAt(si) && p.charAt(j) != '?')
                return false;
        return true;
    }
    ```
