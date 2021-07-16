<!-- prettier-ignore-start -->

> 有一个`m x n` 的二元网格，其中 `1` 表示砖块，`0` 表示空白。砖块 稳定（不会掉落）的前提是：<br>
> 
> - 一块砖直接连接到网格的顶部，或者
> - 至少有一块相邻（4 个方向之一）砖块 稳定 不会掉落时
>
> 给定一个数组 `hits` ，这是需要依次消除砖块的位置。每当消除 `hits[i] = (rowi, coli)` 位置上的砖块时，对应位置的砖块（若存在）会消失，然后其他的砖块可能因为这一消除操作而掉落。一旦砖块掉落，它会立即从网格中消失（即，它不会落在其他稳定的砖块上）<br>
> 返回一个数组 `result` ，其中 `result[i]` 表示第 `i` 次消除操作对应掉落的砖块数目。注意，消除可能指向是没有砖块的空白位置，如果发生这种情况，则没有砖块掉落。<br><br>
Example1:
```
输入：grid = [[1,0,0,0],[1,1,1,0]], hits = [[1,0]]
输出：[2]
解释：
网格开始为：
[[1,0,0,0]，
 [1,1,1,0]]
消除 (1,0) 处的砖块，得到网格：
[[1,0,0,0]
 [0,1,1,0]]
两个砖块不再稳定，因为它们不再与顶部相连，也不再与另一个稳定的砖相邻，因此它们将掉落。得到网格：
[[1,0,0,0],
 [0,0,0,0]]
因此，结果为 [2]
```
Note:
>
-   1 <= grid.length, grid[i].length <= 200
-   0 <= hits.length <= 4 * 10^4
-   所有 (xi, yi) 互不相同

<!-- prettier-ignore-end -->

<br>

并查集适用于计算连通分量，但本题需要「打掉一个掉落多少砖块」转换为「填补一个增加多少砖块」，i.e. 逆向填补被打掉的砖块。考虑几种特殊情形:

-   hit 了一个自始至终为 0 的位置，逆向填补时要忽略这样的 hit
-   hit 了一个曾经为 1 但后来掉了的位置，逆向填补时相当于 1 上填 1，可以不用考虑
-   hit 了一个曾经 hit 过的位置，逆向填补时只有第一次 hit 是有效的，好在输入已经去重

实现细节：

-   只使用路径压缩
-   安排一个哨兵结点表示屋顶，作用在于可以随时访问屋顶结点集的总数，因此不需要一定为根，但填补时需要考虑填补屋顶结点的情况

??? adcodes "Solution X"

    ```java
    class Solution {

        private int M, N;
        public static final int[][] DIRECTIONS = {{0, 1}, {1, 0}, {-1, 0}, {0, -1}};

        public int[] hitBricks(int[][] grid, int[][] hits) {
            this.M = grid.length;
            this.N = grid[0].length;

            // 正向清除砖块，并标记无效的 hit
            for (int k = hits.length - 1; k >= 0; --k) {
                int i = hits[k][0], j = hits[k][1];
                if (grid[i][j] == 0)
                    hits[k][0] = -1;
                else
                    grid[i][j] = 0;
            }

            // 初始化网格
            UFSet ufset = new UFSet(M, N);
            for (int i = 1; i < M; ++i) {
                for (int j = 0; j < N; ++j) {
                    if (grid[i][j] == 0)
                        continue;
                    if (grid[i - 1][j] == 1)
                        ufset.union(i * N + j, (i - 1) * N + j);
                    if (j != N - 1 && grid[i][j + 1] == 1)
                        ufset.union(i * N + j, i * N + j + 1);
                }
            }

            // 逆向填补
            int[] res = new int[hits.length];
            for (int k = hits.length - 1; k >= 0; --k) {
                int i = hits[k][0], j = hits[k][1];
                if (i == -1)
                    continue;
                grid[i][j] = 1;

                int brkNum = 1, addSum = 0;
                for (int[] dir : DIRECTIONS) {
                    int ni = i + dir[0], nj = j + dir[1];
                    if (inArea(ni, nj) && grid[ni][nj] == 1) {
                        boolean flag = ufset.isStable(ni * N + nj);
                        ufset.union(i * N + j, ni * N + nj);
                        int newSize = ufset.getSize(i * N + j);
                        if (!flag) { // 如果新合并的区域未连接屋顶
                            addSum += newSize - brkNum;
                        }
                        brkNum = newSize;
                    }
                }
                if (ufset.isStable(i * N + j))
                    res[k] = addSum;
            }

            return res;
        }

        public boolean inArea(int x, int y) {
            return x >= 0 && x < M && y >= 0 && y < N;
        }

        private class UFSet {

            public int[] nodes, rank, stable;

            public UFSet(int M, int N) {
                int size = M * N;
                nodes = new int[size];
                rank = new int[size];
                stable = new int[size];
                for (int i = 0; i < size; ++i) {
                    nodes[i] = i;
                    rank[i] = 1;
                    if (i < N)
                        stable[i] = 1;
                }
            }

            public void union(int x, int y) {
                int rx = find(x), ry = find(y);
                if (rx == ry)
                    return;
                if (rank[rx] > rank[ry]) {
                    nodes[ry] = rx;
                    rank[rx] += rank[ry];
                } else {
                    nodes[rx] = ry;
                    rank[ry] += rank[rx];
                }
                if ((stable[rx] | stable[ry]) == 1)
                    stable[rx] = stable[ry] = 1;
            }

            public int find(int x) {
                return x == nodes[x] ? x : (nodes[x] = find(nodes[x]));
            }

            public int getSize(int x) {
                return rank[find(x)];
            }

            public boolean isStable(int x) {
                return stable[find(x)] == 1;
            }
        }
    }
    ```

??? adcodes "Solution (standard)"

    ```java
    class Solution {

        private int M, N;
        public static final int[][] DIRECTIONS = {{0, 1}, {1, 0}, {-1, 0}, {0, -1}};

        public int[] hitBricks(int[][] grid, int[][] hits) {
            this.M = grid.length;
            this.N = grid[0].length;


            for (int k = hits.length - 1; k >= 0; --k) {
                int i = hits[k][0], j = hits[k][1];
                if (grid[i][j] == 0)
                    hits[k][0] = -1;
                else
                    grid[i][j] = 0;
            }

            UFSet ufset = new UFSet(M, N);

            for (int j = 0; j < N; ++j)
                if (grid[0][j] == 1)
                    ufset.union(j, M * N);

            for (int i = 1; i < M; ++i) {
                for (int j = 0; j < N; ++j) {
                    if (grid[i][j] == 0)
                        continue;
                    if (grid[i - 1][j] == 1)
                        ufset.union(i * N + j, (i - 1) * N + j);
                    if (j != N - 1 && grid[i][j + 1] == 1)
                        ufset.union(i * N + j, i * N + j + 1);
                }
            }

            int[] res = new int[hits.length];
            for (int k = hits.length - 1; k >= 0; --k) {
                int i = hits[k][0], j = hits[k][1];
                if (i == -1)
                    continue;
                int former = ufset.getCeilNum();
                if (i == 0)
                    ufset.union(j, M * N);
                grid[i][j] = 1;
                for (int[] dir : DIRECTIONS) {
                    int ni = i + dir[0], nj = j + dir[1];
                    if (inArea(ni, nj) && grid[ni][nj] == 1)
                        ufset.union(i * N + j, ni * N + nj);
                }
                int latter = ufset.getCeilNum();
                res[k] = Math.max(0, latter - former - 1);
            }

            return res;
        }

        public boolean inArea(int x, int y) {
            return x >= 0 && x < M && y >= 0 && y < N;
        }

        public static void main(String[] args) {
            Solution s = new Solution();
            int[][] a1 = { { 1, 0, 1 }, { 1, 1, 1 } };
            int[][] hits = { { 0, 0 }, { 0, 2 }, { 1, 1 } };
            int[] res = s.hitBricks(a1, hits);
            for (int i = 0; i < res.length; ++i)
                System.out.print(res[i] + " ");
        }

        private class UFSet {

            public int[] nodes, rank;

            public UFSet(int M, int N) {
                int size = M * N + 1;
                nodes = new int[size];
                rank = new int[size];
                for (int i = 0; i < size; ++i) {
                    nodes[i] = i;
                    rank[i] = 1;
                }
            }

            public void union(int x, int y) {
                int rx = find(x), ry = find(y);
                if (rx == ry)
                    return;
                nodes[rx] = ry;
                rank[ry] += rank[rx];
            }

            public int find(int x) {
                return x == nodes[x] ? x : (nodes[x] = find(nodes[x]));
            }

            public int getCeilNum() {
                return rank[find(rank.length - 1)];
            }
        }
    }
    ```

复杂度：由于仅使用路径压缩，为 $O((\mathrm{len\ of\ \mathsf{hits}} + MN)\cdot \log(MN))$
