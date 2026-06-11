# Chapter 8: Union and Find + Segment Tree（并查集与线段树）

## Part 1: Union and Find（并查集 / 不相交集 ADT）

---

## 1. 等价关系 (Equivalence Relations)

### 定义

**Definition (关系 / Relation)**:
一个关系 $R$ 定义在集合 $S$ 上，如果对于每一对元素 $(a, b)$，$a, b \in S$，$a R b$ 要么为真要么为假。如果 $a R b$ 为真，则称 $a$ 与 $b$ 相关。

**Definition (等价关系 / Equivalence Relation)**:
一个在集合 $S$ 上的关系 $\sim$ 被称为 **等价关系 (equivalence relation)**，当且仅当它在 $S$ 上是：
- **自反的 (Reflexive)**: 对所有 $a \in S$，有 $a \sim a$。
- **对称的 (Symmetric)**: 如果 $a \sim b$，则 $b \sim a$。
- **传递的 (Transitive)**: 如果 $a \sim b$ 且 $b \sim c$，则 $a \sim c$。

**Definition (等价类 / Equivalence Class)**:
集合 $S$ 中的两个成员 $x$ 和 $y$ 被称为在 **同一个等价类 (equivalence class)** 中，当且仅当 $x \sim y$。

---

## 2. 动态等价问题 (The Dynamic Equivalence Problem)

### 问题描述

给定一个等价关系 $\sim$，判断对于任意 $a$ 和 $b$ 是否有 $a \sim b$。

**示例 (Example)**:
给定 $S = \{ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12 \}$ 以及 9 个关系：
$$12 \equiv 4,\ 3 \equiv 1,\ 6 \equiv 10,\ 8 \equiv 9,\ 7 \equiv 4,\ 6 \equiv 8,\ 3 \equiv 5,\ 2 \equiv 11,\ 11 \equiv 12$$

得到的等价类 (equivalence classes) 为：
- $\{ 2, 4, 7, 11, 12 \}$
- $\{ 1, 3, 5 \}$
- $\{ 6, 8, 9, 10 \}$

### 算法框架 (Algorithm Framework)

```
Algorithm:
{
    /* step 1: 读入关系 */
    初始化 N 个不相交的集合;           // Initialize N disjoint sets
    while (读入 a ~ b) {
        if (!(Find(a) == Find(b)))
            Union 这两个集合;           // Union the two sets
    }
    
    /* step 2: 判断 a ~ b */
    while (读入 a 和 b)
        if (Find(a) == Find(b))   output(true);
        else   output(false);
}
```

这是一个 **动态 (Dynamic / On-line)** 算法，因为我们在读入关系的过程中逐步合并等价类。

### 基本概念 (Basic Concepts)

**元素 (Elements)**: $1, 2, 3, \dots, N$

**集合 (Sets)**: $S_1, S_2, \dots$，且满足 $S_i \cap S_j = \emptyset$（当 $i \neq j$）--- **不相交 (disjoint)**

**示例 (Example)**:
$$S_1 = \{ 6, 7, 8, 10 \},\ S_2 = \{ 1, 4, 9 \},\ S_3 = \{ 2, 3, 5 \}$$

### 操作 (Operations)

- **Union(i, j)**: 将 $S_i$ 和 $S_j$ 替换为 $S = S_i \cup S_j$（合并两个集合）
- **Find(i)**: 找到包含元素 $i$ 的集合 $S_k$（查找元素所属的集合）

### 森林表示法 (Forest Representation)

每个集合用一棵树表示，指针从子节点指向父节点 (child → parent)。每个集合的**根节点 (root)** 作为该集合的**代表 (set name / representative)**。

```
示例图形（文字描述）:
        10              4               2
       / | \            | \             | \
      6  8  7           1   9           3   5
```

根节点: 10, 4, 2 分别代表集合 $S_1, S_2, S_3$。

---

## 3. 基本数据结构 (Basic Data Structure)

### Union 操作 (Union Operation)

**思路**: 将一棵树作为另一棵树的子树。即，将一个根节点的父指针指向另一个根节点。

```
S1 ∪ S2 (S2的根指向S1的根):
        10
       / | \
      6  8  7
            |
            4
           / \
          1   9

S2 ∪ S1 (S1的根指向S2的根):
        4
       / \
      1   9
          |
          10
         / | \
        6  8  7
```

### 实现方式 (Implementation)

#### 实现方案 1: Name 数组 (Name Array)
使用一个 `name[ ]` 数组，其中 `name[ k ]` 指向集合 $S_k$。但是这种实现不够高效。

#### 实现方案 2: 父指针数组 (Parent Pointer Array)
使用数组 $S$，其中 `S[element]` 存放的是该元素的父节点索引 (parent index)。

**约定 (Convention)**:
- `S[root] = 0`，表示根节点（没有父节点）
- **集合名称 (set name)** = 根节点的索引 (index of the root)

**示例 (Example)**: 三个集合的数组表示

元素编号从 1 到 N，因此可以作为数组下标。

```
S数组:
S[1] = 4     S[2] = 0     S[3] = 2
S[4] = 0     S[5] = 2     S[6] = 10
S[7] = 10    S[8] = 10    S[9] = 4
S[10] = 0

对应的树结构:
    10              4              2
   / | \           / \            / \
  6  8  7         1   9          3   5
```

**SetUnion 操作**:
```c
void SetUnion(DisjSet S, SetType Rt1, SetType Rt2)
{
    S[Rt2] = Rt1;
}
```
表示 $S_1 \cup S_2 \Rightarrow S_1$ 等价于 `S[4] = 10`（将集合 4 的根指向集合 10 的根）。

**Find 操作**:
```c
SetType Find(ElementType X, DisjSet S)
{
    for (; S[X] > 0; X = S[X])
        ;
    return X;
}
```
沿着父指针向上追溯，直到找到根节点（`S[X] == 0`）。

### 性能分析 (Analysis)

**最坏情况 (Worst Case)**: 当 Union 操作总是将较深的树挂在较浅的树下时，树的深度会退化为 $O(N)$。

```
N → (N-1) → ... → 1
T = Θ(N²)
```

**示例（最坏情况序列）**:
```
union(2, 1), find(1);
union(3, 2), find(1);
...
union(N, N-1), find(1);
```

**结论**: 简单的 Union 和 Find 配对使用时，最坏情况时间复杂度为 $\Theta(N^2)$，性能很差。

### 用 Union-Find 解决等价关系问题的完整算法

对应前面示例 9 个关系：

```
Algorithm:
{
    初始化 S_i = {i} for i = 1, ..., 12;    // Initialize each element as its own set
    for (k = 1; k <= 9; k++) {              /* 对每一对 i ≡ j */
        if (Find(i) != Find(j))
            SetUnion(Find(i), Find(j));
    }
}
```

---

## 4. 智能 Union 算法 (Smart Union Algorithms)

### 按大小合并 (Union-by-Size)

**思路**: 总是将较小的树合并到较大的树中（即改变较小树的根）。

**实现 (Implementation)**: `S[Root] = -size`，初始化为 `-1`。

```c
void SetUnion(DisjSet S, SetType Rt1, SetType Rt2)
{
    /* Rt1 和 Rt2 是根节点 */
    if (S[Rt2] < S[Rt1]) {    /* Rt2 更大 (更负) */
        S[Rt2] += S[Rt1];     /* 更新大小 */
        S[Rt1] = Rt2;         /* Rt2 成为新根 */
    } else {
        S[Rt1] += S[Rt2];
        S[Rt2] = Rt1;
    }
}
```

**Lemma (引理)**: 设 $T$ 是由 Union-by-Size 在有 $N$ 个节点的集合上创建的树，则 $T = O(N)$（更准确地说，树的高度不超过 $\lfloor \log_2 N \rfloor + 1$）。

**证明 (Proof)**: 用归纳法 (induction)。每个元素至多被改变集合名称 $\log_2 N$ 次。因为每次改变集合名称时，元素所在的集合大小至少翻倍（它从较小的集合移到了较大的集合），而集合大小最大为 $N$。

**时间复杂度 (Time Complexity)**: $N$ 次 Union 和 $M$ 次 Find 的操作序列的时间复杂度为 $O(N + M \log_2 N)$。

### 按高度合并 (Union-by-Height / Union-by-Rank)

**思路**: 总是将较浅的树合并到较深的树中。

**实现 (Implementation)**: `S[Root] = -height`，初始化为 `-1`。

高度只在 Union 操作时更新：当两棵树高度相同时，新树的高度增加 1。

详细实现参考教材 Figure 8.13。

---

## 5. 路径压缩 (Path Compression)

### 思路 (Idea)

在执行 Find 操作时，将路径上所有经过的节点直接连接到根节点，从而大大降低后续 Find 操作的代价。

### 递归实现 (Recursive Implementation)

```c
SetType Find(ElementType X, DisjSet S)
{
    if (S[X] <= 0)      /* 如果 X 是根节点 */
        return X;
    else
        return S[X] = Find(S[X], S);   /* 递归查找根节点并压缩路径 */
}
```

### 迭代实现 (Iterative Implementation)

```c
SetType Find(ElementType X, DisjSet S)
{
    ElementType root, trail, lead;
    
    /* 第一阶段：找到根节点 */
    for (root = X; S[root] > 0; root = S[root])
        ;
    
    /* 第二阶段：路径压缩，将所有经过的节点直接指向根 */
    for (trail = X; trail != root; trail = lead) {
        lead = S[trail];
        S[trail] = root;
    }
    
    return root;
}
```

### 注意事项 (Notes)

- 单个 Find 操作可能变慢（因为需要遍历并修改路径），但**一系列 Find 操作的总时间会显著减少**。
- 路径压缩 **与 Union-by-Height 不兼容**，因为路径压缩会改变树的高度。因此使用路径压缩时，将"高度"视为一个估计的**秩 (rank)**，即使用 **Union-by-Rank**。

---

## 6. Union-by-Rank 和路径压缩的最坏情况分析 (Worst-Case Analysis)

### 时间复杂度 (Time Complexity)

**Lemma (Tarjan 引理)**:
设 $T(M, N)$ 为处理 $M \geq N$ 次 Find 和 $N-1$ 次 Union 的混合序列所需的最大时间。则：
$$k_1 M \alpha(M, N) \leq T(M, N) \leq k_2 M \alpha(M, N)$$
其中 $k_1$ 和 $k_2$ 为正的常数。

### 反阿克曼函数 (Inverse Ackermann Function)

**迭代对数 (iterated logarithm) $\log^* N$**:
$$\log^* N = \text{对 N 反复取对数直到结果} \leq 1 \text{的次数}$$

**示例 (Example)**:
$$\log^* 65536 = 4$$
（因为 $\log 65536 = 16$，$\log 16 = 4$，$\log 4 = 2$，$\log 2 = 1$，共 4 次）

$$\log^* 2^{65536} = 5$$
（因为 $\log\log\log\log\log(2^{65536}) = 1$）

**性质 (Property)**: $\alpha(M, N)$（反阿克曼函数 / inverse Ackermann function）增长极度缓慢：
$$\alpha(M, N) \leq O(\log^* N) \leq 4 \ (\text{在实际应用中有意义的范围内})$$

因此，Union-by-Rank + 路径压缩的并查集操作几乎是**常数时间 (constant time)** 的。

### 阿克曼函数 (Ackermann's Function)

阿克曼函数 $A(i, j)$ 是一个快速增长函数 (rapidly growing function)：
- $A(0, j) = 1$（对 $j \geq 1$）
- $A(1, 0) = 2$
- $A(i, 0) = i + 2$（对 $i \geq 2$）
- $A(i, j) = A(i-1, A(i, j-1))$（对 $i \geq 1, j \geq 1$）

反阿克曼函数 $\alpha(M, N)$ 定义为使得 $A(\alpha(M, N), \lfloor M/N \rfloor) > \log N$ 的最小整数 (the smallest integer such that ...)。

---

## Part 2: Segment Tree（线段树）

---

## 1. 线段树的动机 (Motivation)

### 问题 (Problem)

给定一个数组 `A[1000000]`，需要频繁地计算任意范围 $[L, R]$ 内元素的和（**范围查询 / range query**）。

### 朴素解法 (Naive Solution)

```c
ElementType Query(ElementType A[], int L, int R)
{
    ElementType sum = 0;
    for (int i = L; i <= R; ++i)
        sum += A[i];
    return sum;
}
```

- 时间复杂度: $T(N) = O(N)$
- 当查询操作频繁时，$O(N)$ 的复杂度无法接受

### 线段树的优势 (Advantages of Segment Tree)

线段树可以在 $O(\log N)$ 时间内完成**范围查询 (range query)** 和**单点更新 (point update)** 操作，构建时间为 $O(N)$。

---

## 2. 线段树的结构 (Structure)

### 定义 (Definition)

线段树是一棵**完全二叉树 (complete binary tree)**，每个节点代表数组的一个区间（段）。

### 示例 (Example)

给定数组 `A[5] = {7, 2, 5, 8, 3}`：

```
                      25 [0,4]
                     /        \
                14 [0,2]     11 [3,4]
                /     \       /     \
             9 [0,1]  5[2]  8[3]   3[4]
             /    \
          7[0]   2[1]
```

- **叶节点 (leaf node)**：对应单个数组元素，存储元素的值
- **内部节点 (internal node)**：对应一个区间，存储该区间内元素的聚合值（此处为和）
- 树的数组存储索引 (array index)：从上到下，从左到右编号

### 性质 (Properties)

- 线段树是**完全二叉树 (complete binary tree)**，可以用数组存储
- 空间复杂度: $S(N) = O(2N - 1)$
- 节点的子节点索引：`2*node`（左子节点 / left child），`2*node+1`（右子节点 / right child）
- 节点存储的值：该节点代表区间的聚合值 (aggregate value)

---

## 3. 构建线段树 (Build)

### 算法 (Algorithm)

```c
void Build(int node, int start, int end)
{
    // 基本情况：叶节点
    if (start == end) {
        tree[node] = A[start];
        return;
    }
    
    // 递归步骤：分割并构建子节点
    int mid = (start + end) / 2;
    Build(2 * node, start, mid);
    Build(2 * node + 1, mid + 1, end);
    
    // 合并逻辑：子节点的和
    tree[node] = tree[2 * node] + tree[2 * node + 1];
}
```

### 构建过程 (Build Process)

1. **叶节点 (leaf node)** (`start == end`): 直接存储 `A[start]` 的值
2. **内部节点 (internal node)**: 先递归构建左子树 `[start, mid]` 和右子树 `[mid+1, end]`，然后合并两个子节点的值
3. 合并操作可以是加和、取最大值、取最小值等

### 时间复杂度 (Time Complexity)

- $T(N) = O(N)$
- 构建只需运行一次

---

## 4. 范围查询 (Range Query)

### 查询规则 (Query Rules)

在进行范围查询 $[L, R]$ 时，每个节点有 3 种情况：

1. **无重叠 (No Overlap)**: 节点的范围完全在查询范围之外（`R < start || end < L`）
   - 返回不影响结果的标识值（例如求和查询返回 0）
2. **完全重叠 (Total Overlap)**: 节点的范围完全在查询范围之内（`L <= start && end <= R`）
   - 直接返回该节点存储的值
3. **部分重叠 (Partial Overlap)**: 只有部分重叠
   - 递归查询左右子节点，合并结果

### 查询示例 (Query Example)

查询 `[L=2, R=4]`：

```
                      25 [0,4]   ← 部分重叠 (Partial Overlap)
                     /        \
                14 [0,2]     11 [3,4]   ← 两个都是部分重叠
                /           /     \
             9 [0,1]     8[3]   3[4]   ← 完全重叠 (Total Overlap): 8+3=11
             /
          7[0]   2[1]   ← 无重叠 (No Overlap): 返回0
```

- 左子树 `[0,2]`: 部分重叠，继续递归
  - 左子 `[0,1]`: 无重叠，返回 0
  - 右子 `[2]`: 完全重叠，返回 5
  - 返回 `0 + 5 = 5`
- 右子树 `[3,4]`: 部分重叠，继续递归
  - 左子 `[3]`: 完全重叠，返回 8
  - 右子 `[4]`: 完全重叠，返回 3
  - 返回 `8 + 3 = 11`
- 最终结果: `5 + 11 = 16`

### 查询算法 (Query Algorithm)

```c
int Query(int node, int start, int end, int L, int R)
{
    // Case 1: 无重叠（节点完全在查询范围之外）
    if (R < start || end < L) {
        return 0;
    }
    
    // Case 2: 完全重叠（节点完全在查询范围之内）
    if (L <= start && end <= R) {
        return tree[node];
    }
    
    // Case 3: 部分重叠（需要深入两个子节点）
    int mid = (start + end) / 2;
    int left_sum = Query(2 * node, start, mid, L, R);
    int right_sum = Query(2 * node + 1, mid + 1, end, L, R);
    
    return left_sum + right_sum;
}
```

### 时间复杂度 (Time Complexity)

$$T(N) = O(\log N)$$

每次查询最多访问 $O(\log N)$ 个节点，因为树高为 $\log N$，且每一层最多访问 4 个节点。

---

## 5. 单点更新 (Point Update)

### 更新过程 (Update Process)

更新操作将数组 `A[idx]` 的值修改为 `val`，并同步更新线段树中所有相关节点的值。

1. 从根节点开始递归查找包含 `idx` 的节点
2. 到达叶节点后，更新 `tree[node] = val`
3. 回溯 (backtrack) 时，沿途更新所有祖先节点

### 更新示例 (Update Example)

将 `A[3]` 从 8 更新为 9：

**Step 1: 查找并更新叶节点**
```
                      25 [0,4]
                     /        \
                14 [0,2]     11 [3,4]   ← idx=3 在右子树
                /     \       /     \
             9 [0,1]  5[2]  8[3]   3[4] ← idx=3 在左子树
             /    \
          7[0]   2[1]
```

到达叶节点 `[3]`，将值从 8 改为 9。

**Step 2: 回溯更新祖先**
```
                      26 [0,4]     ← 25+1=26
                     /        \
                14 [0,2]     12 [3,4]   ← 11+1=12
                /     \       /     \
             9 [0,1]  5[2]  9[3]   3[4] ← 8→9
             /    \
          7[0]   2[1]
```

更新 `[3]` 的父节点 `[3,4]`：从 `8+3=11` 变为 `9+3=12`
更新 `[3,4]` 的父节点 `[0,4]`：从 `14+11=25` 变为 `14+12=26`

### 更新算法 (Update Algorithm)

```c
void Update(int node, int start, int end, int idx, int val)
{
    // 基本情况：找到叶节点
    if (start == end) {
        tree[node] = val;
        return;
    }
    
    // 递归步骤：根据 idx 的位置向左或向右
    int mid = (start + end) / 2;
    if (start <= idx && idx <= mid) {
        // 索引在左子节点
        Update(2 * node, start, mid, idx, val);
    } else {
        // 索引在右子节点
        Update(2 * node + 1, mid + 1, end, idx, val);
    }
    
    // 回溯步骤：根据子节点的新值更新当前节点
    tree[node] = tree[2 * node] + tree[2 * node + 1];
}
```

### 时间复杂度 (Time Complexity)

$$T(N) = O(\log N)$$

---

## 6. 线段树的应用与扩展 (Applications and Extensions)

### 聚合操作 (Aggregate Operations)

线段树不限于求和操作，适用于任何**范围聚合操作 (range aggregate operation)**：
- **最小值 (min)**: `tree[node] = min(tree[2*node], tree[2*node+1])`
- **最大值 (max)**: `tree[node] = max(tree[2*node], tree[2*node+1])`
- **平均值 (average)**: 需要同时存储和与数量
- **GCD (最大公约数 / greatest common divisor)**: `tree[node] = gcd(tree[2*node], tree[2*node+1])`

查询时，Case 1 返回对应聚合操作的**中性值 (identity value)**：
- 求和: 返回 `0`
- 取最小值: 返回 `INF`
- 取最大值: 返回 `-INF`
- 取 GCD: 返回 `0`

### 懒标记 (Lazy Propagation) [自学内容 / Self-Study]

对于**范围更新 (Range Update)**（例如：将索引 2 到 1000 的所有数都加上 10），如果使用单点更新逐个更新，需要 $O(N \log N)$ 的时间，在数据量大时不可接受。

**懒标记思想 (Idea of Lazy Tag)**:
- 当进行范围更新时，不立即更新范围内的所有叶节点
- 而是标记那些被完全覆盖的节点（设置 lazy tag）
- 当需要查询或进一步更新时，才将 lazy tag 向下传递（push / propagate）

通过懒标记，范围更新的时间复杂度也能达到 $O(\log N)$。

---

## 总结表格 (Summary Table)

| 操作 (Operation) | 朴素方法 (Naive) | 线段树 (Segment Tree) | 并查集朴素 (Naive DSU) | 并查集优化 (Optimized DSU) |
|------|---------|--------|--------------|--------------|
| Build / Init | - | $O(N)$ | $O(N)$ | $O(N)$ |
| Query / Find | $O(N)$ | $O(\log N)$ | $O(N)$ | $O(\alpha(N))$ |
| Update / Union | $O(1)$ | $O(\log N)$ | $O(1)$ | $O(\alpha(N))$ |

其中 $\alpha(N)$ 是反阿克曼函数 (inverse Ackermann function)，在实际数据规模下 $\leq 4$。

### 并查集智能算法对比 (Smart Union Algorithms Comparison)

| 算法 (Algorithm) | Union 策略 (Strategy) | 树高 (Tree Height) | 时间复杂度 (Time Complexity) |
|------|-----------|------|-----------|
| 朴素 Union (Naive Union) | 任意 (Arbitrary) | $O(N)$ | $O(N^2)$ |
| Union-by-Size | 小树挂到大树 (Smaller → Larger) | $O(\log N)$ | $O(N + M\log N)$ |
| Union-by-Height | 浅树挂到深树 (Shorter → Taller) | $O(\log N)$ | $O(N + M\log N)$ |
| Union-by-Rank + 路径压缩 (Path Compression) | 小秩挂到大秩 + 路径压缩 (Smaller Rank → Larger Rank + PC) | 几乎常数 (Nearly Constant) | $O(M\alpha(M,N))$ |

---

## 考点总结 (Exam Summary)

### 并查集 (Union and Find)

1. **等价关系定义 (Equivalence Relation Definition)**: 自反 (reflexive)、对称 (symmetric)、传递 (transitive)
2. **并查集的基本操作 (Basic Operations)**: Union（合并）、Find（查找）
3. **数组表示法 (Array Representation)**: `S[element] = parent`，根节点的 `S[root]` 为负数或无父节点（0）
4. **Union-by-Size 和 Union-by-Height**: 理解策略 (strategy) 和复杂度分析 (analysis)
5. **Union-by-Size 的引理证明 (Lemma Proof)**: 归纳法 (induction)，每个元素最多被合并 $\log_2 N$ 次
6. **路径压缩 (Path Compression)**: 递归 (recursive) 和迭代 (iterative) 两种实现
7. **最坏情况上界 (Worst-Case Upper Bound)**: $O(M\alpha(M,N))$ 和迭代对数 $\log^* N$ (iterated logarithm)

### 线段树 (Segment Tree)

1. **线段树的性质 (Properties)**: 完全二叉树 (complete binary tree)，每个节点代表数组的一个区间 (interval / segment)
2. **构建 (Build)**: 递归分割区间直到叶节点，$T(N) = O(N)$
3. **查询 (Query)**: 三种情况（无重叠 no overlap、完全重叠 total overlap、部分重叠 partial overlap），$T(N) = O(\log N)$
4. **更新 (Update)**: 递归查找叶节点并回溯 (backtrack)，$T(N) = O(\log N)$
5. **适用场景 (Applications)**: 任何范围聚合操作 (range aggregate operation: sum, min, max, gcd 等)
6. **数组索引 (Array Indexing)**: 根节点索引为 1，左子 = `2*node`，右子 = `2*node+1`
7. **懒标记 (Lazy Propagation)**: 用于高效范围更新 (range update)，自学内容 (self-study)
