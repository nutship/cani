### 1. 概述

可以通过运行 $|V|$ 次单源最短路算法:

-   Dijkstra: &ensp; 复杂度为 $O(V^3 + \mathit{VE}) = O(V^3)$ 或 $O(\mathit{VE\ \lg V})$，适用于稀疏图 (无负权边)
-   Bellman-Ford: &ensp; 复杂度为 $O(V^2 E)$，对稠密图复杂度可达 $O(V^4)$，往往不可接受

而对于所有结点对的最短路算法:

-   Floyd-Warshall: &ensp; 时间 $O(n^3)$，空间 $O(n^2)$，依赖于邻接矩阵

### 2. Floyd-Warshall 算法

#### (1). 原理

设 $G=(\lbrace 1,2,...,n \rbrace, E)$, 该算法依赖于邻接矩阵表示

$$
w_{ij} =
\begin{cases}
0 &\text{if } i=j \\\\
\text{weight of edge }i \to j & \text{if } i\ne j \text{ and } (i,j) \in E\\\\
\infty & \text{if } i \ne j \text{ and } (i,j) \notin E
\end{cases}
$$

令 $d^k(i,j)$ 表示 $i$ 到 $j$ 的最短路，该最短路的所有中间结点在 $\lbrace 1,2,...,k\rbrace$ 内，则有

$$
d^k(i,j) =
\begin{cases}
w_{ij} & \text{if } k=0 \\\\
\min\lbrace\ d^{k-1}(i,j),\ d^{k-1}(i,k) + d^{k-1}(k,j)\ \rbrace & \text{else}
\end{cases}
$$

由此，该算法没有特别的限制，没有负环即可

<font class="ps%">

```c linenums="1"
Floyd-Warshall(w):
    d = w
    for k from 0 to n-1:
        for i from 0 to n-1:
            for j from 0 to n-1:
                if d[i][j] > d[i][k] + d[k][j]:
                    d[i][j] = d[i][k] + d[k][j]

```

</font>

在 Floyd-Warshall 算法的第 7 行，可能已经有 $\mathsf{d[i][k]}=d^{k}(i,k)$ 或 $\mathsf{d[k][j]}=d^{k}(k,j)$，但由于这两条路径以结点 $k$ 开头或结尾，$k$ 一定不在最短路的中间结点集合中 (最短路无环)，因此有 $d^{k-1}(i,k)=d^{k}(i,k)$ 和 $d^{k-1}(k,j)=d^{k}(k,j)$，算法的正确性依然成立

#### (2). 有向图的传递闭包

定义 $G$ 的传递闭包为 $G^{\*} = (V, E^{\*})$，其中 $E^{\*}=\lbrace (i,j):\ \text{if G contains an edge from }i\text{ to }j\rbrace$，则可以套用 Floyd-Warshall 的模板

$$
t_{ij}^0 =
\begin{cases}
0 & \text{if } i \ne j \text{ and } (i,j) \notin E \\\\
1 & \text{if } i = j \text{ or } (i,j) \in E
\end{cases}
$$

$$
t_{ij}^k = t_{ij}^{k-1} \lor (t_{ik}^{k-1} \land t_{kj}^{k-1})
$$
