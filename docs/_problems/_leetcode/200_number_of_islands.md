<!-- prettier-ignore-start -->

> 给你一个由  `1`（陆地）和 `0`（水）组成的的二维网格，请你计算网格中岛屿的数量。<br>
> 岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。<br>
> 此外，你可以假设该网格的四条边均被水包围。
> <br><br>
Example1:
```
输入：grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
输出：1
```
Example2:
```
输入：grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
输出：3
```
Note:
>
-   1 <= grid.length, grid[0].length <= 300
>
> (Medium)

<!-- prettier-ignore-end -->

如果用 DFS / BFS 做且可以修改原数组的话，在全是陆地的情况下需要时间 $O(MN)$，空间 $O(MN)$ / $O(\min(M, N))$。使用并查集不需要额外修改原数组，时间空间均为 $O(MN)$

??? adcodes "Solution"

    ```java
    class Solution {

        private int[] nodes, rank;
        private int islandsNum = 0;

        public int numIslands(char[][] grid) {

            int M = grid.length, N = grid[0].length;
            nodes = new int[M * N];
            rank = new int[M * N];
            for (int i = 0; i < M; ++i) {
                for (int j = 0; j < N; ++j) {
                    int nodeIdx = i * N + j;
                    if (grid[i][j] == '1') {
                        islandsNum++;
                        nodes[nodeIdx] = nodeIdx;
                    }
                }
            }

            for (int i = 0; i < M; ++i) {
                for (int j = 0; j < N; ++j) {
                    if (grid[i][j] == '0')
                        continue;
                    if (j != N - 1 && grid[i][j + 1] == '1')
                        union(i * N + j, i * N + j + 1);
                    if (i != M - 1 && grid[i + 1][j] == '1')
                        union(i * N + j, (i + 1) * N + j);
                }
            }

            return islandsNum;
        }

        public void union(int x, int y) {
            int rx = find(x), ry = find(y);
            if (rx == ry)
                return;
            if (rank[ry] > rank[rx])
                nodes[rx] = ry;
            else
                nodes[ry] = rx;
            if (rank[rx] == rank[ry])
                ++rank[rx];
            islandsNum--;
        }

        public int find(int x) {
            return x == nodes[x] ? x : (nodes[x] = find(nodes[x]));
        }

    }
    ```
