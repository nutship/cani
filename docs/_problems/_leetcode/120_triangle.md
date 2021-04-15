<!-- prettier-ignore-start -->

> 给定一个三角形 triangle ，找出自顶向下的最小路径和。<br>
> 每一步只能移动到下一行中相邻的结点上。相邻的结点 在这里指的是 下标 与 上一层结点下标 相同或者等于 上一层结点下标 + 1 的两个结点。也就是说，如果正位于当前行的下标 i ，那么下一步可以移动到下一行的下标 i 或 i + 1 。<br><br>
```
输入：triangle = [[2],[3,4],[6,5,7],[4,1,8,3]]
输出：11
解释：如下面简图所示：
   2
  3 4
 6 5 7
4 1 8 3
自顶向下的最小路径和为 11（即，2 + 3 + 5 + 1 = 11）。
```
Note:
>
-   1 <= triangle.length <= 200
-   triangle[0].length == 1
-   triangle[i].length == triangle[i - 1].length + 1
-   -10^4 <= triangle[i][j] <= 10^4

<!-- prettier-ignore-end -->

<br>

经典 dp 问题

-   自顶向下: `dp[i][j] = min { dp[i - 1][j], dp[i - 1][j - 1] } + triangle[i][j]` <br>
-   自底向上: `dp[i][j] = min { dp[i + 1][j], dp[i + 1][j + 1] } + triangle[i][j]`, 表示从最低层某一点到 `triangle[i][j]` 的最短路长，代码写起来更简洁

??? adcodes "triangle"

    ```java
    public int minimumTotal(List<List<Integer>> triangle) {
        Integer[] dp = new Integer[triangle.get(triangle.size() - 1).size()];
        triangle.get(triangle.size() - 1).toArray(dp);
        for (int i = triangle.size() - 2; i >= 0; --i) {
            List<Integer> nodes = triangle.get(i);
            for (int j = 0; j < nodes.size(); ++j)
                dp[j] = min(dp[j], dp[j + 1]) + nodes.get(j);
        }
        return dp[0];
    }
    ```
