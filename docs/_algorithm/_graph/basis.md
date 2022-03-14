### 1. 广度优先搜索

白色表示未探索，灰色表示刚被探索并入队，黑色表示被探索且子结点也全被探索

```C
for each u ∈ G.V - {s}
    u.color = WHITE
    u.d = ∞
    u.π = Null
s.color = GRAY, s.d = 0, s.π = Null
EnQueue(s, Q)
while Q is not empty:
    u = DeQueue(Q)
    for each v ∈ G.Adj[u]:
        if v.color == WHITE:
            v.color = GRAY
            v.d = u.d + 1
            v.π = u
            EnQueue(v, Q)
    u.color = BLACK
```

时间复杂性为 $O(V+E)$，如果每条边均为 1 个单位，BFS 还可以计算最短路，直观来看：

-   先入队的结点的 `d` 值大于后入队的
-   入队时的 `d` 一定是最短路长

### 2. 深度优先搜索

白色表示未探索，灰色表示刚被探索，黑色表示被探索且后代结点也全被探索

```C
DFS-Visit(G, u):
    time = time + 1
    u.d = time
    u.color = GRAY
    for each v ∈ G.Adj[u]:
        if v.color = WHITE:
            v.π = u
            DFS-Visit(G, v)
        else if v.color = GRAY:
            // find a backward edge
        else:
            ...
    u.color = BLACK
    time = time + 1
    u.f = time

DFS(G):
    for each u ∈ G.V:
        u.color = WHITE
        u.π = Null
    time = 0
    for each u ∈ G.V:
        if u.color = WHITE:
            DFS-Visit(G, u)
```

DFS 主要有以下性质：

-   括号化结构：可以通过递归记录结点刚被发现以及结束的时间
    <img src="../img/pstr.png">
-   边的分类：可以通过第一次搜索到 $(u,v)$ 时 $v$ 的颜色为 $G=(V,E)$ 的边分派一个类别
    -   树边：深度优先森林中的边 (白色)
    -   后向边：将 $u$ 连到祖先结点 $v$ 的边 ($v$ 是灰色，说明还在 $v$ 的时间片段内，i.e. 在深度优先树中 $u$ 是 $v$ 后代且指向 $v$)
    -   前向边: 将 $u$ 连到后代结点 $v$ 的边 (黑色)
    -   横向边：其他边 (黑色)

### 3. 拓扑排序

#### (1). Kahn 算法

剥皮算法，反向 BFS

```C
L = Empty list that will contain the sorted elements
S = Set of all nodes with no incoming edges
while S in non-empty:
    n = remove a node n from S
    Insert(n, L)
    for each m in G.adj[n]:
        remove (n, m) from G
        if m has no other incoming edges:
            Insert(m, S)
if G has edges:
    return ERROR
else:
    return L
```

其核心在于维护一个 0 入度点集，复杂度 $O(V+E)$

#### (2). DFS

由 DFS 树的括号化结构 (标注时间的深度优先森林)，直观上可以逆序构建拓扑序集

-   首先，假设深度优先树中一个结点即将变黑，其后代已经逆序加入拓扑集，从它引出的边已经分好类，不讨论已在树中的树边
-   由它引出的边不可能是后向边:
    -   有向图 $G=(V,E)$ 无环 $\Longleftrightarrow$ $G$ 没有 DFS 的后向边
    -   $\Leftarrow$ 的简单理解：给定一个有向树只要不插入后向边就还无环
-   那么只可能是指向黑结点的 前向边或横向边，由于黑结点已加入拓扑集，该结点也可以

```C
Graph G[MAXN];
int color[MAXN];
Vertices topo;

bool dfs(int u) {
    color[u] = -1;
    for (int v : G[u]) {
        if (color[v] < 0)
            return false;
        else if (color[v] == 0)
            if (!dfs(v))
                return false;
    }
    color[u] = 1;
    topo.push(u);
    return true;
}

bool toposort() {
    for (int u = 0; u < n; u++)
        if (color[u] == 0)
            if (!dfs(u))
                return false;
    reverse(topo);
    return true;
}
```
