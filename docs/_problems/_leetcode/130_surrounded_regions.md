<!-- prettier-ignore-start -->

> 给你一个 m x n 的矩阵 board ，由若干字符 `X` 和 `O` ，找到所有被 `X` 围绕的区域，并将这些区域里所有的 `O` 用 `X` 填充。
> <br><br>
Example1:
```
输入：board = [["X","X","X","X"],
              ["X","O","O","X"],
              ["X","X","O","X"],
              ["X","O","X","X"]]
输出：        [["X","X","X","X"],
              ["X","X","X","X"],
              ["X","X","X","X"],
              ["X","O","X","X"]]
解释：被围绕的区间不会存在于边界上，换句话说，任何边界上的 'O' 都不会被填充为 'X'。 任何不在边界上，或不与
边界上的 'O' 相连的 'O' 最终都会被填充为 'X'。如果两个元素在水平或垂直方向相邻，则称它们是“相连”的。
```
Example2:
```
输入：board = [["X"]]
输出：[["X"]]
```
Note:
>
-   1 <= board.length, board[0].length <= 200
>
> (Medium)

<!-- prettier-ignore-end -->

<br>

和 <a href="../../_leetcode/200_number_of_islands">200. 岛屿总数</a> 类似

-   DFS / BFS: 从位于边界上的 `O` 开始遍历并标记，时间为 $O(MN)$，空间 $O(MN)$ \ $O(\min(m,n))$，针对此题考虑通常情况时间只需要计算边缘因此时间会低一些，但需要额外修改原数组
-   并查集: 不需要修改原数组，引入 edge guard node 判断是否连接边缘，时间空间均为 $O(MN)$

??? adcodes "DFS"

    ```java
    class Solution {

        private int M, N;

        public void dfs(int x, int y, char[][] board) {
            if (x < 0 || x == M || y < 0 || y == N || board[x][y] != 'O')
                return;
            board[x][y] = 'E';
            dfs(x + 1, y, board);
            dfs(x - 1, y, board);
            dfs(x, y + 1, board);
            dfs(x, y - 1, board);
        }

        public void solve(char[][] board) {
            M = board.length;
            N = board[0].length;
            for (int j = 0; j < N; ++j) {
                if (board[0][j] == 'O')
                    dfs(0, j, board);
                if (board[M - 1][j] == 'O')
                    dfs(M - 1, j, board);
            }
            for (int i = 1; i < M - 1; ++i) {
                if (board[i][0] == 'O')
                    dfs(i, 0, board);
                if (board[i][N - 1] == 'O')
                    dfs(i, N - 1, board);
            }
            for (int i = 0; i < M; ++i)
                for (int j = 0; j < N; ++j)
                    if (board[i][j] == 'E')
                        board[i][j] = 'O';
                    else if (board[i][j] == 'O')
                        board[i][j] = 'X';
        }
    }
    ```

??? adcodes "Union-Find Set"

    ```java
    class Solution {

        private int[] nodes, rank;

        public void solve(char[][] board) {
            int M = board.length, N = board[0].length;
            nodes = new int[M * N + 1];
            rank = new int[M * N + 1];
            for (int i = 0; i < M; ++i) {
                for (int j = 0; j < N; ++j) {
                    nodes[i * N + j] = i * N + j;
                    if (board[i][j] == 'O' && isEdge(i, j, M, N))
                        union(i * N + j, M * N);
                }
            }

            for (int i = 0; i < M; ++i) {
                for (int j = 0; j < N; ++j) {
                    if (board[i][j] == 'X')
                        continue;
                    if (j != N - 1 && board[i][j + 1] == 'O')
                        union(i * N + j, i * N + j + 1);
                    if (i != M - 1 && board[i + 1][j] == 'O')
                        union(i * N + j, (i + 1) * N + j);
                }
            }

            for (int i = 0; i < M; ++i) {
                for (int j = 0; j < N; ++j) {
                    int edgeNode = find(M * N);
                    if (board[i][j] == 'O' && find(i * N + j) != edgeNode)
                        board[i][j] = 'X';
                }
            }
        }

        public boolean isEdge(int x, int y, int M, int N) {
            return x == 0 || x == M - 1 || y == 0 || y == N - 1;
        }

        public void union(int x, int y) {
            int rx = find(x), ry = find(y);
            if (rx == ry)
                return;
            if (rank[rx] < rank[ry])
                nodes[rx] = ry;
            else
                nodes[ry] = rx;
            if (rank[rx] == rank[ry])
                rank[rx] += 1;
        }

        public int find(int x) {
            return x == nodes[x] ? x : (nodes[x] = find(nodes[x]));
        }
    }
    ```
