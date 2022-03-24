### 1. 01 背包

#### (1). 基本思路

给定 $n$ 种物品和一个背包，物品 $n_i$ 的重量为 $w_i$，价值为 $v_i$，背包容量为 $C$，如何选择背包中的物品，使总价值最大。该问题可形式化为:

$$
\mathrm{Input:}\ W,\ V, \ C,\ i, \enspace \mathrm{Output:}\ \lbrace x_0, ..., x_{n-1}\rbrace \\\\
s.t. \max\sum_0^{n-1} v_i x_i,\ \sum_0^{n-1} w_i x_i < C,\ x_i \in \lbrace 0,1 \rbrace
$$

该问题的结构特征由 $i,C$ 描述，$dp(i,C)$ 表示可选物品是 $x_0$ 到 $x_i$ 时的价值，则 $\lbrace x_0, ..., x_{i-1} \rbrace$ 一定是 $dp(i-1, C-x_{i}w_{i})$ 的最优解，问题只在于第 $i$ 件物品放与不放，因此 0-1 背包的转移方程为

$$
dp(i, C) = \max \lbrace \ dp(i-1, C),\enspace dp(i-1, C-w_i)+v_i \ \rbrace
$$

初始化: &ensp; $dp[0][0...w_0] = 0,\enspace dp[0][w_0 ... C]=v_0$

#### (2). 空间优化

由于 $dp(i, C)$ 只涉及 $dp(i-1, C)$ 和 $dp(i-1, C-w)$ 两个状态，空间上只需要逆向遍历一个一维数组

### 2. 完全背包

和 01 背包不同之处在于，每种物品有无限个，即 $x_i \in \lbrace {0, 1, ..., \lfloor C/w_i \rfloor } \rbrace$

#### (1). 基本思路

按照最基本的想法，每一步考虑 $x_i$ 的所有取值情况，可得:

$$
dp(i, C) = \max \lbrace \ dp(i-1, C-kw_i) + kv_i \ | \ 0 \leq kw_i \leq C \ \rbrace
$$

求解每个状态的复杂度不再是常数，总复杂度难以接受

#### (2). 进阶思路

如果在状态递推时考虑「添加一个 或 不添加物品 $n_i$」，而不是「物品 $n_i$ 添加几个」，可得

$$
dp(i, C) = \max \ \lbrace \ dp(i-1, C),\ dp(i, C-w_i)+v_i \ \rbrace
$$

和 01 背包的状态方程不同处在于向 $dp(i, C-w_i)$ 递推，表示 $n_i$ 添加一个。需要注意：

-   $dp[0][0...C]$ 初始化后依然需要状态递推
-   使用一维数组空间优化时只能正向遍历

#### (3). 转换为 0-1 背包

思路为把一个物品拆分成多个，每个物品在 $\lbrace 0,1 \rbrace$ 中取值。如果把第 $i$ 种物品转化为 $\lfloor C/w_i \rfloor$ 个价值不变的物品，复杂度相较基本思路并没有改变。更高效的拆分方式为:

$$
n_i(w_i, v_i) \Longrightarrow \lbrace\ n_{ik}(w_i2^k, v_i2^k) \ |\ k\in N \mathrm{\ and\ } w_i2^k < C \ \rbrace
$$

这里用到了二进制的思想: 不管一个物品取几件，一定可以表示为 $2^k$ 的和。这样就把一种物品拆分为 $O(\log (\lfloor C/w_i \rfloor ))$ 个物品

### 附: 问法的变化

#### (1). 恰好装满

由状态方程可知，装满与否的区别只在于初始化:

-   如果没有恰好装满的限制，只需将 $dp[0][0...w_0]$ 全初始化为 0 即可
-   如果限制恰好装满，$dp[0][0]$ 初始化为 0，表示容量为 0 且价值为 0，在恰好装满的限制下是一个合法解；$dp[0][w_0]$ 初始化为 $v_0$；其余状态全部初始化为 $-\infty$，表示未定义

#### (2). 求方案总数

由于状态转移方程已经考察了所有可能的方案，只需简单修改一下即可

$$
dp(i, C) = \mathrm{sum} \lbrace \ dp(i-1, C),\enspace dp(i-1, C-w_i)\ \rbrace
$$

其中 $dp(0, 0)=1$。如果需要求最优方案总数，则需要新开一个状态方程的数组。

#### (3). 二维背包

二维背包是指每个背包有两个限制条件 (例如重量和体积)，选择物品必须要满足两个条件，只需要在状态数组上加一维即可。

### 相关问题

<div class="outerlink">
<a href="../../_leetcode/416_partition_equal_subset_sum">416. Partition Equal Subset Sum</a> (01 背包) <br>
<a href="../../_leetcode/474_ones_and_zeroes">474. Ones and Zeroes</a> (二维 01 背包) <br>
<a href="../../_leetcode/494_target_sum">494. target sum</a> (类似 01 背包) <br>
<a href="../../_leetcode/322_coin_change">322. Coin Change</a> (完全背包 + 恰好装满 + 最小价值) <br>
<a href="../../_leetcode/518_coin_change_2">518. Coin Change 2</a> (完全背包 + 求方案数 + 恰好装满) <br>
</div>
