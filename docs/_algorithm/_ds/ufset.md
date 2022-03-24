### 1. 并查集概述

并查集有三种操作：

-   `Make-Set(x)`: 建立唯一成员为 $\sf x$ 的新集合
-   `Union(x, y)`: 合并集合 $\sf S_x$ 和 $\sf S_y$
-   `Find(x)`: 返回集合 $\sf S_x$ 的代表

衡量并查集的复杂性需要考虑三种操作的总体排列 $I$，因此，定义 `Make-Set` 总数为 $m$，且认为 $I$ 的前 $m$ 个操作一定是 `Make-Set`. $I$ 包含的操作总数为 $n$

### 2. 链表实现

<img src="./img/ufsetlinked.png" width=600>

`Make-Set` 和 `Find` 复杂度均为 $O(1)$，但 `Union` 需要更新指针，最坏情况下 $m$ 个 `Union` 需要 $\Theta(m^2)$. 而如果使用启发式策略，让短链表向长链表合并，考虑到

> <ktb></ktb>
>
> > 执行完 $m$ 个 `Make-Set` 后，对任意一个链表 x, x 第 1 次被合并时会产生长至少为 2 的链表，以此类推，x 第 $\lceil \lg k \rceil$ 次合并时会产生长至少为 $k$ 的链表

因此每个链表至多被合并 $\lg n$ 次，因此启发式策略的复杂度为 $O(n + m\lg m)$

### 3. 有根树实现

有根树的普通结点指向父结点，根结点指向自身，可以用有根树森林表示并查集，三种操作分别对应：

-   `Make-Set(x)`: 让 `x` 指向自身
-   `Find(x)`: 沿 `x` 上溯到根
-   `Union(x)`: 让一棵树的根指向另一棵的根

```C
void makeset() {
    for (int i = 0; i < MAXN; ++i)
        nodes[i] = i;
}

void find(int x) {
    return x == nodes[x] ? x : find(nodes[x]);
}

void union(int x, int y) {
    nodes[find(x)] = find(y);
}
```

仅如此有根树可能会形成直链，其最坏复杂性也为 $O(n^2)$，但通过不同的启发策略，可使 $n$ 个操作的复杂度近乎线性: $O(n\cdot \alpha(m))$，$\alpha$ 是一个增长很慢的函数，通常有 $\alpha(m)\leq 4$

#### 按秩合并

每棵树的根维护一个秩，表示高度的一个上界

-   `Make-Set` 后，每个结点 (树) 的秩为 0
-   相同秩的树合并，任选一个作为新根，且新根的秩加一
-   不同秩的树合并，大秩根成为新根，且秩不变

```C
void Union(int x, int y) {
    int rx = find(x), ry = find(y);
    if (rank[rx] > rand[ry])
        nodes[ry] = nodes[rx];
    else
        nodes[rx] = nodes[ry];
    if (rank[rx] == rank[ry] && rx != ry)
        ++rank[ry];
}
```

#### 路径压缩

在 `Find-Set` 的过程中，(回溯时) 使查找路径上每个结点指向根，且不改变任何结点的秩

```C
int find(int x) {
    return x == nodes[x] ? x : (nodes[x] = find(nodes[x]));
}
```
