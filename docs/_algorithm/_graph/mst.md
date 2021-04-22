### 1. 最小生成树的形成

#### (1). 算法框架

Kruskal 和 Prim 算法都使用贪心的框架求解 MST，区别在于贪心的策略不同

<font class="ps%">

```c linenums="1"
Generic-MST(G, w):
    A = ∅
    while A does not form a spanning tree:
        find an edge that is safe for A
        A = A ∪ {(u, v)}
    return A
```

</font>

#### (2). 一些定义

-   安全边: &ensp; 不破坏 `Generic-MST` 的贪心循环不变式性质的边 `(u,v)`
-   切割 (cut): &ensp; $(S,\ V-S)$ 是 $V$ 的一个划分
-   横跨 (cross): &ensp; 如果 $(u,v)\in E$ 的两个端点分别位于 $S$ 和 $V-S$ 中，则称 $(u,v)$ 横跨切割 $(S,\ V-S)$
-   如果边集 $A$ 不存在横跨 $(S,\ V-S)$ 的边，称 $A$ 尊重 该切割
-   横跨一个切割的所有边中，权重最小的边称为 light edge

#### (3). 安全边的选取

> <ktb></ktb>
> 无向图 $G=(V,E)$，$A\subset E$ 且 $A$ 是 $G$ 的一颗 MST 的子集。设 $(S, V-S)$ 是 $G$ 尊重 $A$ 的任一切割，$(u,v)$ 是横跨 $(S, V-S)$ 的一条 light edge，则 $(u, v)$ 对于 $A$ 是安全的
>
> > 设 $T$ 是一颗 MST 且 $(u,v)\notin T$。则在 $T$ 的从 $u$ 到 $v$ 的路径上一定存在一条边 $(x,y)$ cross $(S,V-S)$，令 $T'=T-\lbrace (x,y) \rbrace \cup \lbrace (u,v) \rbrace$，则 $w(T')\leq w(T)$，由于 $T$ 是 MST，因此 $T'$ 也是。<br> &emsp;<font class="i_n_" id="nodes of S are black, nodes of V-S are white"><img src="../img/mst1.png" width=360> </font> <br>
> > 由于 $A\subseteq T$ 且 $(x,y)\notin A$，所以 $A\subseteq T'$；由于 $A\cup (u,v)\subseteq T'$，因此 $(u,v)$ 对于 $A$ 是安全的

### 2. Kruskal 算法

Kruskal 算法的集合 `A` 是一个森林，初始有 $|V|$ 个森林。每次找到连接两个森林且最短的边 $(u,v)$，设被连接的两个森林为 $C_1$ 和 $C_2$，那么 $(u,v)$ 一定是 $(C_1,V-C_1)$ 的 light edge

<font class="ps%">

```c linenums="1"
Kruskal(G, w):
    A = ∅
    for each vertex v ∈ G.V:
        Make-Set(v)
    sort the edges of G.E into nondecreasing order by weight
    for each edge(u,v) ∈ G.E (taken in nondecreasing order):
        if Find-Set(u) ≠ Find-Set(v):
            A = A ∪ {(u, v)}
            Union(u, v)
    return A
```

</font>

复杂度 $O(E\lg E + (V+E)\alpha(V))$，可以表示为 $O(E\lg E)$ 或 $O(E\lg V)$

### 3. Prim 算法

Prim 算法的集合 `A` 是一颗树，初始只有一个根结点。最小堆维护 $(A, V-A)$ 的横切边，每一轮选取其中的 light edge，然后更新权重，算法结构和 Dijkstra 类似。

```python linenums="1"
Prim(G, w, r):
    for each u ∈ G.V:
        u.key = ∞
        u.π = Null
    r.key = 0
    Q = G.V
    while Q ≠ ∅:
        u = Extract-Min(Q)
        for each v ∈ G.Adj[u]:
            if v ∈ Q and w(u, v) < v.key:
                v.key = w(u, v)
                v.π = u
    return {(v, v.π) | v ∈ V - {r}}
```

</font>

复杂度为 $O(V\lg V + E\lg V)$
