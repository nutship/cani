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

相当于 <a href="../10_regular_exp_matching" style="color:blue">10. 正则匹配</a> 的简化版

$$
dp[i][j] =
\begin{cases}
dp[i-1][j-1] \ \mathrm{\ and\ } \ (s[i-1] \mathrm{\ equals\ } p[j-1]) & \mathrm{if}\ p[j-1] \ne \* \\\\
dp[i][j-1] \ \mathrm{\ or\ } \ dp[i-1][j] \ \mathrm{\ or\ } \ dp[i-1][j-1] & \mathrm{else}
\end{cases}
$$
