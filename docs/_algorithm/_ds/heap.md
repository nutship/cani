### 1. 堆

二叉堆是用数组表示的完全二叉树 [^1]。以最小堆为例，满足定义:

-   除根以外所有结点 $\sf i$ 满足 $\sf i.value \leq parent(i).value$

完全二叉树访问父子结点:

-   (i from 1): `left(i) = 2*i`, `right(i) = 2*i+1`, `father(i) = i/2`, `fp = N / 2`
-   (i from 0): `left(i) = 2*i+1`, `right(i) = 2*i+2`, `father(i) = (i-1)/2`, `fp = N / 2 - 1`

#### (1). 向下保持堆的性质

假设左右子树 `left(i)` 和 `right(i)` 均满足最小堆的性质，而 `i` 和它们的关系不确定，通过 `MinHeapify(i, heap)` 向下维护最小堆的性质:

-   空出 `i` 的位置，如果 `left(i)` 和 `right(i)` 的最小值比 `i.value` 小，则向下递归

```java
public void MinHeapify(int i) {
    if (i > this.size)
        return;
    int father = i, child = 2 * i + 1;
    int value = this.nodes[father];
    while (child < this.size) {
        if (child + 1 < this.size && this.nodes[child + 1] < this.nodes[child])
            child++;
        if (value < this.nodes[child])
            break;
        this.nodes[father] = this.nodes[child];
        father = child;
        child = 2 * child + 1;
    }
    this.nodes[father] = value;
}
```

#### (2). 向上保持堆的性质

如果减小 `i.value`，向下的性质可以保持，但向上不一定，则向上递归保持最小堆性质

```java
public void DecreaseKey(int i, int key) {
    if (key >= this.nodes[i])
        return;
    while (i != 0 && this.nodes[(i - 1) / 2] > key) {
        this.nodes[i] = this.nodes[(i - 1) / 2];
        i = (i - 1) / 2;
    }
    this.nodes[i] = key;
}
```

#### (3). 建堆

从第一个非叶节点 `fp(i)` 开始，向下调整堆

```java
public void BuildMinHeap() {
    for (int i = this.size / 2 - 1; i >= 0; --i)
        MinHeapify(i);
}
```

高为 $k$ 的层有 $2^{h-k}$ 个结点，高为 $h$ 的满二叉树有 $2^{h-1}$ 个结点，有

$$
\sum_{k=0}^h k \cdot 2^{h-k} = 2^h \sum_{k=0}^h \frac{k}{2^k} = 2^h (2- \frac{h + 2}{2^h}) \leq 2n
$$

故建堆复杂度为 $O(n)$

### 2. 优先队列

优先队列底层调用堆的实现，需要提供的接口:

-   `ExtractMin(Q)`: 弹出堆顶元素，把最后一个元素置于堆顶，然后向下调整堆
-   `Insert(key, Q)`: 置于堆底，然后向上调整
-   如果需要同时弹出和插入两种操作，可以弹出后插在堆顶，可以节约一个 $\log$ 的时间

[^1]: 完全二叉树：除最后一层外的其余层都是满的，并且最后一层要么是满的，要么在右边缺少连续若干节点
