<!-- prettier-ignore-start -->

> 给你两个单词 `word1` 和 `word2`，请你计算出将 `word1` 转换成 `word2` 所使用的最少操作数 <br>
> 你可以对一个单词进行如下三种操作：
>
-   插入一个字符
-   删除一个字符
-   替换一个字符
>
> Example1:
```
输入：word1 = "horse", word2 = "ros"
输出：3
解释：
horse -> rorse (将 'h' 替换为 'r')
rorse -> rose (删除 'r')
rose -> ros (删除 'e')
```
> Example2:
```
输入：word1 = "intention", word2 = "execution"
输出：5
解释：
intention -> inention (删除 't')
inention -> enention (将 'i' 替换为 'e')
enention -> exention (将 'n' 替换为 'x')
exention -> exection (将 'n' 替换为 'c')
exection -> execution (插入 'u')
```
Note:
>
-   `0 <= word1.length, word2.length <= 50`
-   `word1` and `word2` consist of lowercase English letters.

<!-- prettier-ignore-end -->

<br>

以 `"horse"` 和 `"rosy"` 为例，编辑距离更准确的说是这样的过程:

$$
\enspace
\begin{array}{lll}
\text{Operation}         &amp; \mathrm{word1}           &amp; \mathrm{word2} \\\\
\hline
\textit{initial strings}      &amp; \underline horse           &amp; \text{\textunderscore} \\\\
\text{replace by } r          &amp; h\underline orse           &amp; r\text{\textunderscore} \\\\
\text{copy}                   &amp; ho\underline rse           &amp; ro\text{\textunderscore} \\\\
\text{delete}                 &amp; hor\underline se           &amp; ro\text{\textunderscore} \\\\
\text{copy}                   &amp; hors\underline e           &amp; ros\text{\textunderscore} \\\\
\text{delete}                 &amp; horse\underline\           &amp; ros\text{\textunderscore} \\\\
\text{insert } y              &amp; horse\underline\          &amp; rosy\text{\textunderscore}
\end{array}
$$

各种操作的规律可总结为:

<font class="t_a%0&0_b%30_h%3&0">

| $\text{Operation}$ | $\text{effect}$ | $\text{cost}$ |
| ------------------ | --------------- | ------------- |
| $\text{replace}$   | `i+=1, j+=1`    | 1             |
| $\text{insert}$    | `j+=1`          | 1             |
| $\text{delete}$    | `i+=1`          | 1             |
| $\text{copy}$      | `i+=1, j+=1`    | 0             |

</font>

本题的状态方程:

$$
dp[i][j] = \min\lbrace \ dp[i][j-1]+1,\enspace dp[i-1][j]+1,\enspace dp[i-1][j-1] + (\mathrm{word1}[i] \ne \mathrm{word2}[j]) \ \rbrace
$$

??? adcodes "solution"

    ```java
    public int minDistance(String word1, String word2) {
        int M = word1.length(), N = word2.length();
        int[][] dp = new int[M + 1][N + 1];
        for (int j = 0; j <= N; ++j)
            dp[0][j] = j;
        for (int i = 1; i <= M; ++i) {
            dp[i][0] = i;
            for (int j = 1; j <= N; ++j) {
                dp[i][j] = Math.min(dp[i][j - 1], dp[i - 1][j]) + 1;
                dp[i][j] = Math.min(dp[i][j],
                        dp[i - 1][j - 1] + ((word1.charAt(i - 1) == word2.charAt(j - 1)) ? 0 : 1));
            }
        }
        return dp[M][N];
    }
    ```
