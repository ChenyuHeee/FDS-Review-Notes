# Chapter 9: Graph Algorithms 图算法

---

## §1 Definitions 基本定义

### 1.1 图的定义

- **Graph 图**: $G(V, E)$，其中：
  - $V = V(G)$: 顶点的有限非空集合（finite nonempty set of vertices）
  - $E = E(G)$: 边的有限集合（finite set of edges）

- **Undirected Graph 无向图**: $(v_i, v_j) = (v_j, v_i)$，表示同一条边。

- **Directed Graph (Digraph) 有向图**: $\langle v_i, v_j \rangle \neq \langle v_j, v_i \rangle$
  - $v_i$ 为 **tail**（尾）
  - $v_j$ 为 **head**（头）

- **Restrictions 限制**:
  1. **Self loop 自环** is illegal（不允许自环）
  2. **Multigraph 多重图** is not considered（不考虑多重边）

- **Complete Graph 完全图**: a graph that has the **maximum number of edges**
  - 无向完全图: 边数为 $C_n^2 = n(n-1)/2$
  - 有向完全图: 边数为 $n(n-1)$

### 1.2 基本术语

| Term | Definition |
|------|-----------|
| **Adjacent 邻接** | 无向图中：$v_i$ 和 $v_j$ 相邻（adjacent） |
| **Incident 关联** | $(v_i, v_j)$ 关联于 $v_i$ 和 $v_j$ |
| **Adjacent to/from** | 有向图中：$v_i$ adjacent **to** $v_j$；$v_j$ adjacent **from** $v_i$；$\langle v_i, v_j \rangle$ 关联于 $v_i$ 和 $v_j$ |
| **Subgraph 子图** | $G' \subseteq G$ 满足 $V(G') \subseteq V(G)$ 且 $E(G') \subseteq E(G)$ |
| **Path 路径** | 从 $v_p$ 到 $v_q$ 的顶点序列 $\{v_p, v_{i1}, v_{i2}, \dots, v_{in}, v_q\}$，要求相邻顶点间存在边 |
| **Length of Path 路径长度** | 路径上**边的数量**（number of edges on the path） |
| **Simple Path 简单路径** | $v_{i1}, v_{i2}, \dots, v_{in}$ 都是不同的（distinct） |
| **Cycle 环** | 满足 $v_p = v_q$ 的简单路径（simple path with $v_p = v_q$） |

### 1.3 连通性

- **Connected 连通**（无向图）：$v_i$ 和 $v_j$ 之间存在一条路径。$v_i$ and $v_j$ in an undirected $G$ are **connected** if there is a path from $v_i$ to $v_j$ (and hence there is also a path from $v_j$ to $v_i$).
- **Connected Graph 连通图**：An undirected graph $G$ is **connected** if **every pair** of distinct $v_i$ and $v_j$ are connected.
- **Component (Connected Component) 连通分量**：the **maximal** connected subgraph（极大连通子图）

### 1.4 特殊图

| Type | Definition |
|------|-----------|
| **Tree 树** | a graph that is **connected** and **acyclic**（连通且无环） |
| **DAG** | a **Directed Acyclic Graph**（有向无环图） |
| **Strongly Connected 强连通**（有向图） | for **every pair** of $v_i$ and $v_j$ in $V(G)$, there exist **directed paths** from $v_i$ to $v_j$ **and** from $v_j$ to $v_i$ |
| **Weakly Connected 弱连通**（有向图） | If the graph is connected **without direction** to the edges, then it is said to be **weakly connected** |
| **Strongly Connected Component (SCC) 强连通分量** | the **maximal** subgraph that is strongly connected |

### 1.5 度 (Degree)

- **Degree (度) $degree(v)$**: number of edges incident to $v$

- 对于**有向图**，分为：
  - **in-degree 入度**: 指向该顶点的边数
  - **out-degree 出度**: 从该顶点出发的边数
  - **degree(v) = in-degree(v) + out-degree(v)**

- **示例**: $in-degree(v) = 3$, $out-degree(v) = 1$, $degree(v) = 4$

- **性质**: 给定有 $n$ 个顶点和 $e$ 条边的图 $G$，有：
  $$\sum_{v \in V} degree(v) = 2e$$

---

### 1.6 图的表示 (Representation of Graphs)

#### Adjacency Matrix 邻接矩阵

- $adj\_mat[n][n]$ 定义，$n \geq 1$
- $adj\_mat[i][j] = 1$（或权重）如果存在边 $(v_i, v_j)$，否则为 0

**存储优化（无向图）**:
- 若 $G$ 是无向图，则 $adj\_mat[\;][\;]$ 是对称的（symmetric）
- 可以只存储**一半**矩阵来节省空间
- 使用**一维数组**存储：
  $$adj\_mat[n(n+1)/2] = \{a_{11}, a_{21}, a_{22}, \dots, a_{n1}, \dots, a_{nn}\}$$
- 下标计算：$a_{ij}$ 的索引为 $(i \times (i - 1) / 2 + j)$

**复杂度**:
- 空间复杂度: $O(n^2)$
- 若要判断图是否连通，需检查所有边，时间复杂度 $O(n^2)$
- 适合**稠密图**（dense graph）

#### Adjacency Lists 邻接表

- 用**链表**替换矩阵的每一行
- **示例**：
  - 图：顶点 0-1-2（0-1, 0-2, 1-2）
  - `graph[0]` -> 1 -> 2
  - `graph[1]` -> 0 -> 2
  - `graph[2]` -> 1
  - 注：每个列表中节点的顺序不重要（The order of nodes in each list does not matter）

- **空间**（无向图）：
  $$S = n \text{ heads} + 2e \text{ nodes} = (n + 2e) \text{ ptrs} + 2e \text{ ints}$$

- **时间复杂度**：
  - $degree(i) = \text{number of nodes in } graph[i]$（无向图）
  - 遍历所有边的复杂度 $T = O(n + e)$

- 若为**有向图**，还需要找入度：
  - **Method 1**: 添加**逆邻接表**（inverse adjacency lists）
    - 示例：顶点 0->1, 1->0, 1->2
    - `inv[0]` -> 1
    - `inv[1]` -> 0
    - `inv[2]` -> 1
  - **Method 2**: 使用**多重表**（Multilist, Ch 3.2）表示 $adj\_mat[i][j]$
    - 节点结构：`tail i | head j | row ptr | column ptr`

#### Adjacency Multilists 邻接多重表

- 在邻接表中，每条边 $(i, j)$ 有两个节点，分别放在 `graph[i]` 和 `graph[j]` 中
- 合并两个节点为一个：

```
graph[i] -> [node] -> ...
graph[j] -> [node] -> ...
```

- 节点结构：`mark | v1 | v2 | next_v1 | next_v2`
  - **Advantage**: 在检查一条边后需要标记（mark）它，然后找下一条边时，这个表示法很方便

- **示例**：
  - `graph[0]` -> Edge(0,1) -> Edge(0,2)
  - `graph[1]` -> Edge(0,1) -> Edge(1,2) -> Edge(2,3)
  - `graph[2]` -> Edge(0,2) -> Edge(1,2) -> Edge(2,3)
  - `graph[3]` -> Edge(2,3)

#### Weighted Edges 加权边

- 邻接矩阵: $adj\_mat[i][j] = weight$（权重）
- 邻接表/多重表：在节点中添加一个 `weight` 字段

---

## §2 Topological Sort 拓扑排序

### 2.1 AOV Network (Activity on Vertex Network)

- **AOV Network**: 有向图 $G$，其中 $V(G)$ 表示**活动**（activities，如课程），$E(G)$ 表示**优先关系**（precedence relations）
  - 示例：$\langle C1, C3 \rangle$ 表示 $C1$ 是 $C3$ 的先修课程（prerequisite）

- **Predecessor 前驱**: 如果存在从 $i$ 到 $j$ 的路径，则 $i$ 是 $j$ 的**前驱**（predecessor）
- **Immediate Predecessor 直接前驱**: $\langle i, j \rangle \in E(G)$
  - 相应地，$j$ 是 $i$ 的**后继**（successor）或**直接后继**（immediate successor）

- **Partial Order 偏序关系**: 一种**传递性**（transitive）且**反自反**（irreflexive）的优先关系
  - **Transitive 传递性**: $i \to k, k \to j \Rightarrow i \to j$
  - **Irreflexive 反自反**: $i \to i$ 是不可能的
  - 如果优先关系是自反的，则存在 $i$ 使得 $i$ 是自己的前驱——这意味着 $i$ 必须在开始之前完成，这是不可能的
  - 因此，一个可行的项目（feasible project）**必须**是反自反的

- **关键结论**: Feasible AOV network **must be a DAG** (directed acyclic graph) —— 可行的 AOV 网络必须是无环有向图

### 2.2 Topological Order 拓扑序

**【Definition】** A **topological order** is a **linear ordering** of the vertices of a graph such that, for any two vertices $i$, $j$, if $i$ is a predecessor of $j$ in the network then $i$ precedes $j$ in the linear ordering.

**定义**：拓扑序是图中顶点的一个线性排序，使得对于任意两个顶点 $i$, $j$，如果 $i$ 是 $j$ 的前驱，则 $i$ 在排序中出现在 $j$ 之前。

**示例**：计算机科学学位的一种可能课程安排：
- 列出所有课程，按先修关系排序，确保每门课都在其先修课程之后

> **Note**: The topological orders **may not be unique** for a network. 拓扑序可能不唯一。例如，满足计算机科学学位要求的课程安排可以有多种方式。

### 2.3 拓扑排序算法

**Goal**: Test an AOV for feasibility, and generate a topological order if possible. （测试 AOV 的可行性，如果可能则生成一个拓扑序。）

#### Algorithm 1: 朴素版本 $O(|V|^2)$

```c
void Topsort( Graph G )
{
    int  Counter;
    Vertex  V, W;
    for ( Counter = 0; Counter < NumVertex; Counter ++ ) {
        V = FindNewVertexOfDegreeZero( );  /* O(|V|) */
        if ( V == NotAVertex ) {
            Error( "Graph has a cycle" );
            break;
        }
        TopNum[ V ] = Counter; /* or output V */
        for ( each W adjacent to V )
            Indegree[ W ]--;
    }
}
/* T = O( |V|^2 ) */
```

- 每次循环需要扫描所有顶点找入度为 0 的顶点（$O(|V|)$），共 $|V|$ 次 → $O(|V|^2)$

#### Algorithm 2: 改进版本 $O(|V| + |E|)$ （使用队列）

**Improvement**: Keep all the unassigned vertices of degree 0 in a special box (queue or stack).

将所有未分配的入度为 0 的顶点放入一个特殊容器（队列或栈）中。

```c
void Topsort( Graph G )
{
    Queue  Q;
    int  Counter = 0;
    Vertex  V, W;
    Q = CreateQueue( NumVertex );  MakeEmpty( Q );
    for ( each vertex V )
        if ( Indegree[ V ] == 0 )   Enqueue( V, Q );
    while ( !IsEmpty( Q ) ) {
        V = Dequeue( Q );
        TopNum[ V ] = ++ Counter; /* assign next */
        for ( each W adjacent to V )
            if ( --Indegree[ W ] == 0 )  Enqueue( W, Q );
    }  /* end-while */
    if ( Counter != NumVertex )
        Error( "Graph has a cycle" );
    DisposeQueue( Q ); /* free memory */
}
/* T = O( |V| + |E| ) */
```

---

#### 完整实例追踪（基于 Textbook 图 9.4）

**初始入度表**（来自幻灯片示例）:

| Vertex | v1 | v2 | v3 | v4 | v5 | v6 | v7 |
|--------|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| Indegree | 0 | 1 | 2 | 3 | 1 | 3 | 2 |

**执行过程**（使用队列）:

| Step | Dequeue | Counter | 更新邻接点入度 | 入队 |
|:----:|:-------:|:-------:|:--------------:|:----:|
| 1 | v1 | 1 | v2: 1→0, v4: 3→2 | v2 |
| 2 | v2 | 2 | v4: 2→1, v5: 1→0 | v5 |
| 3 | v5 | 3 | v7: 2→1 | (none) |

**结果**: 队列为空，但 Counter=3 < NumVertex=7。剩余顶点 v3, v4, v6, v7 入度均大于 0，说明图中存在环（cycle），不是一个可行的 AOV 网络。

---

## §3 Shortest Path Algorithms 最短路径算法

**问题描述**: Given a digraph $G = (V, E)$, and a cost function $c(e)$ for $e \in E(G)$. The length of a path $P$ from source to destination is $\sum c(e_i)$ (weighted path length).

给定一个有向图 $G = (V, E)$ 和边的代价函数 $c(e)$，路径 $P$ 从源点到目的地的长度是路径上所有边的代价之和。

---

### 3.1 Single-Source Shortest-Path Problem 单源最短路径

**Input**: A weighted graph $G = (V, E)$ and a distinguished vertex $s$ (source).
**Output**: Find the **shortest weighted path** from $s$ to **every other vertex** in $G$.

**Negative-cost cycle 负代价环**:
- 图中可能存在负代价环（negative-cost cycle）
- 如果存在负代价环，路径长度可以无限减小
- **Note**: If there is **no negative-cost cycle**, the shortest path from $s$ to $s$ is defined to be **zero**.

---

### 3.2 Unweighted Shortest Paths 无权最短路径

#### 基本思路 (Breadth-First Search)

- 使用**广度优先搜索**（Breadth-first search）
- 按距离递增的顺序访问顶点：从源点开始，距离为 0，然后是距离为 1 的顶点，距离为 2 的顶点，依此类推

**示例**:
- 0: $v_3$（源点）
- 1: $v_1$ 和 $v_6$
- 2: $v_2$ 和 $v_4$
- 3: $v_5$ 和 $v_7$

#### 数据结构: Table 表

| Field | Description |
|-------|-------------|
| `Table[i].Dist` | 从 $s$ 到 $v_i$ 的距离，初始化为 $\infty$（除 $s$ 为 0） |
| `Table[i].Known` | 1 表示 $v_i$ 已检查，0 表示未检查 |
| `Table[i].Path` | 用于追踪路径，初始化为 0 |

#### Algorithm 1: 朴素版本 $O(|V|^2)$

```c
void Unweighted( Table T )
{
    int  CurrDist;
    Vertex  V, W;
    for ( CurrDist = 0; CurrDist < NumVertex; CurrDist ++ ) {
        for ( each vertex V )
            if ( !T[ V ].Known && T[ V ].Dist == CurrDist ) {
                T[ V ].Known = true;
                for ( each W adjacent to V )
                    if ( T[ W ].Dist == Infinity ) {
                        T[ W ].Dist = CurrDist + 1;
                        T[ W ].Path = V;
                    } /* end-if Dist == Infinity */
            } /* end-if !Known && Dist == CurrDist */
    }  /* end-for CurrDist */
}
/* T = O( |V|^2 ) */
```

> **性质**: If $V$ is unknown yet has $Dist < Infinity$, then $Dist$ is either $CurrDist$ or $CurrDist + 1$.

**最坏情况**: 一条长链的图，每次循环只标记一个顶点 → $O(|V|^2)$

#### Algorithm 2: 改进版本 $O(|V| + |E|)$（使用队列）

```c
void Unweighted( Table T )
{   /* T is initialized with the source vertex S given */
    Queue  Q;
    Vertex  V, W;
    Q = CreateQueue( NumVertex );  MakeEmpty( Q );
    Enqueue( S, Q ); /* Enqueue the source vertex */
    while ( !IsEmpty( Q ) ) {
        V = Dequeue( Q );
        T[ V ].Known = true; /* not really necessary */
        for ( each W adjacent to V )
            if ( T[ W ].Dist == Infinity ) {
                T[ W ].Dist = T[ V ].Dist + 1;
                T[ W ].Path = V;
                Enqueue( W, Q );
            } /* end-if Dist == Infinity */
    } /* end-while */
    DisposeQueue( Q ); /* free memory */
}
/* T = O( |V| + |E| ) */
```

#### 实例追踪

图结构：
- $v_3 \to v_1, v_6$
- $v_1 \to v_2, v_4$
- $v_6 \to v_4, v_7$
- $v_4 \to v_2, v_5$
- $v_2 \to v_5$
- $v_7 \to v_5$

源点 $s = v_3$。

**初始表**:

| Vertex | Dist | Path |
|:------:|:----:|:----:|
| v1 | $\infty$ | 0 |
| v2 | $\infty$ | 0 |
| v3 | **0** | 0 |
| v4 | $\infty$ | 0 |
| v5 | $\infty$ | 0 |
| v6 | $\infty$ | 0 |
| v7 | $\infty$ | 0 |

**队列操作过程**:

| Dequeue | 邻接点 | Dist 更新 | 入队 |
|:-------:|:------:|:---------:|:----:|
| v3 | v1, v6 | v1.D=1, v6.D=1 | v1, v6 |
| v1 | v2, v4 | v2.D=2, v4.D=2 | v2, v4 |
| v6 | v4, v7 | v7.D=2（v4已有Dist=2不变） | v7 |
| v2 | v5 | v5.D=3 | v5 |
| v4 | v2, v5 | (均已设置) | - |
| v7 | v5 | (已设置) | - |
| v5 | - | - | - |

**最终表**:

| Vertex | Dist | Path |
|:------:|:----:|:----:|
| v1 | 1 | v3 |
| v2 | 2 | v1 |
| v3 | 0 | 0 |
| v4 | 2 | v1 |
| v5 | 3 | v2 |
| v6 | 1 | v3 |
| v7 | 2 | v6 |

---

### 3.3 Dijkstra's Algorithm 迪杰斯特拉算法（加权最短路径）

#### 核心思想（贪心法 Greedy Method）

Let $S = \{ s \text{ and } v_i\text{'s whose shortest paths have been found} \}$

For any $u \notin S$, define $distance[u] = \text{minimal length of path } \{ s \to (v_i \in S) \to u \}$.

If the paths are generated in **non-decreasing order**, then:

1. **性质 1**: The shortest path must go through **ONLY** $v_i \in S$（最短路径必须只经过 $S$ 中的顶点）
   - **证明**: 如果该性质不成立，则路径上必然存在一个不在 $S$ 中的顶点 $w$，而 $w$ 的距离小于 $u$ 的距离，矛盾。

2. **性质 2**: $u$ is chosen so that $distance[u] = \min\{ w \notin S \mid distance[w] \}$
   - 如果 $u$ 不唯一，可以任选一个

3. **性质 3**: 如果 $distance[u_1] < distance[u_2]$ 并且我们将 $u_1$ 加入 $S$，则 $distance[u_2]$ 可能改变。新的更短路径可能经过 $u_1$:
   $$distance'[u_2] = distance[u_1] + length(\langle u_1, u_2 \rangle)$$

#### 伪代码

```c
void Dijkstra( Table T )
{   /* T is initialized by Figure 9.30 on p.303 */
    Vertex  V, W;
    for ( ; ; ) {
        V = smallest unknown distance vertex;   /* O(|V|) */
        if ( V == NotAVertex )
            break;
        T[ V ].Known = true;
        for ( each W adjacent to V )
            if ( !T[ W ].Known )
                if ( T[ V ].Dist + Cvw < T[ W ].Dist ) {
                    /* Decrease T[ W ].Dist to T[ V ].Dist + Cvw */
                    T[ W ].Dist = T[ V ].Dist + Cvw;
                    T[ W ].Path = V;
                } /* end-if update W */
    } /* end-for( ; ; ) */
}
/* Does NOT work for edges with negative costs */
```

#### 实例追踪

图结构（与上例相同，但边有权重）：
- $v_1 \to v_2 (2), v_4 (1)$
- $v_2 \to v_4 (3), v_5 (10)$
- $v_3 \to v_1 (4), v_6 (5)$
- $v_4 \to v_3 (2), v_5 (2), v_6 (8), v_7 (4)$
- $v_5 \to v_7 (6)$
- $v_6 \to v_7 (1)$

源点 $s = v_1$。

**初始表**:

| Vertex | Known | Dist | Path |
|:------:|:-----:|:----:|:----:|
| v1 | F | **0** | 0 |
| v2 | F | $\infty$ | 0 |
| v3 | F | $\infty$ | 0 |
| v4 | F | $\infty$ | 0 |
| v5 | F | $\infty$ | 0 |
| v6 | F | $\infty$ | 0 |
| v7 | F | $\infty$ | 0 |

**执行过程**:

| Step | V (min unknown Dist) | Known | 更新邻接点 |
|:----:|:--------------------:|:-----:|:----------:|
| 1 | v1 (Dist=0) | v1=T | v2: 0+2=2 < $\infty$ → Dist=2, Path=v1; v4: 0+1=1 < $\infty$ → Dist=1, Path=v1 |
| 2 | v4 (Dist=1) | v4=T | v3: 1+2=3 < $\infty$ → Dist=3, Path=v4; v5: 1+2=3 < $\infty$ → Dist=3, Path=v4; v6: 1+8=9 < $\infty$ → Dist=9, Path=v4; v7: 1+4=5 < $\infty$ → Dist=5, Path=v4 |
| 3 | v2 (Dist=2) | v2=T | v4: 2+3=5 > 1, 不变; v5: 2+10=12 > 3, 不变 |
| 4 | v3 (Dist=3) | v3=T | v1: 已known; v6: 3+5=8 < 9 → Dist=8, Path=v3 |
| 5 | v5 (Dist=3) | v5=T | v7: 3+6=9 > 5, 不变 |
| 6 | v7 (Dist=5) | v7=T | (无更新) |
| 7 | v6 (Dist=8) | v6=T | v7: 8+1=9 > 5, 不变 |

**最终表**:

| Vertex | Known | Dist | Path |
|:------:|:-----:|:----:|:----:|
| v1 | T | 0 | 0 |
| v2 | T | 2 | v1 |
| v3 | T | 3 | v4 |
| v4 | T | 1 | v1 |
| v5 | T | 3 | v4 |
| v6 | T | 8 | v3 |
| v7 | T | 5 | v4 |

**最短路径**:
- $v_1 \to v_1$: 0
- $v_1 \to v_2$: $v_1 \to v_2$, Dist=2
- $v_1 \to v_3$: $v_1 \to v_4 \to v_3$, Dist=3
- $v_1 \to v_4$: $v_1 \to v_4$, Dist=1
- $v_1 \to v_5$: $v_1 \to v_4 \to v_5$, Dist=3
- $v_1 \to v_6$: $v_1 \to v_4 \to v_3 \to v_6$, Dist=8
- $v_1 \to v_7$: $v_1 \to v_4 \to v_7$, Dist=5

#### 时间复杂度分析

**Implementation 1**: 扫描整个表找最小未知距离顶点
- 找最小未知距离: $O(|V|)$
- 总复杂度: $T = O(|V|^2 + |E|)$
- 适合**稠密图**（dense graph）

**Implementation 2**: 使用优先队列（Priority Queue）
- 找最小未知距离: DeleteMin → $O(\log|V|)$
- 更新距离:
  - **Method 1**: DecreaseKey → $O(\log|V|)$
    - $T = O(|V|\log|V| + |E|\log|V|) = O(|E|\log|V|)$
  - **Method 2**: 将更新后的 $W$ 直接插入优先队列
    - 持续执行 DeleteMin 直到找到一个未知顶点
    - $T = O(|E|\log|V|)$，但需要 $|E|$ 次 DeleteMin 和 $|E|$ 空间
- 适合**稀疏图**（sparse graph）

**其他改进**: Pairing heap (Ch.12), Fibonacci heap (Ch.11)

> **重要限制**: Dijkstra 算法**不能处理负权边**
> ```c
> /* Does NOT work for edges with negative costs */
> ```

---

### 3.4 Graphs with Negative Edge Costs 含负权边的图

> **关于增加常数 $\Delta$ 的想法**:
> "why don't we simply add a constant $\Delta$ to each edge and thus remove negative edges?"
> — Too simple, and na&iuml;ve...（过于简单天真）
>
> **反例**: 增加常数后会改变最短路径的选择，因为路径长度不同，边数也不同

#### WeightedNegative 算法

```c
void WeightedNegative( Table T )
{   /* T is initialized by Figure 9.30 on p.303 */
    Queue  Q;
    Vertex  V, W;
    Q = CreateQueue( NumVertex );  MakeEmpty( Q );
    Enqueue( S, Q ); /* Enqueue the source vertex */
    while ( !IsEmpty( Q ) ) {
        V = Dequeue( Q );
        for ( each W adjacent to V )
            if ( T[ V ].Dist + Cvw < T[ W ].Dist ) {
                T[ W ].Dist = T[ V ].Dist + Cvw;
                T[ W ].Path = V;
                if ( W is not already in Q )
                    Enqueue( W, Q );
            } /* end-if update */
    } /* end-while */
    DisposeQueue( Q ); /* free memory */
}
/* T = O( |V| x |E| ) */
/* each vertex can dequeue at most |V| times */
/* no longer once per edge */
```

**复杂度分析**:
- $T = O(|V| \times |E|)$
- 每个顶点最多出队 $|V|$ 次（不只是每条边一次）

> **重要**: negative-cost cycle will cause **indefinite loop**（负代价环会导致无限循环）

---

### 3.5 Acyclic Graphs 无环图

如果图是无环的（acyclic），可以按**拓扑序**（topological order）选择顶点。

**原理**: When a vertex is selected, its distance can no longer be lowered without any incoming edges from unknown nodes.

当一个顶点被选择时，由于没有来自未知顶点的入边，其距离不能再被降低。

- $T = O(|E| + |V|)$
- **不需要优先队列**（no priority queue is needed）

#### AOE Networks (Activity On Edge Networks)

AOE 网络用于**调度项目**（scheduling a project）。

**基本概念**:
- $a_i$ ::= activity（活动）
- $v_j$ ::= vertex, signals the **completion** of $a_i$（标记活动完成）

| Term | Definition |
|------|-----------|
| **EC[j]** | **Earliest completion time** for node $v_j$（最早完成时间） |
| **LC[j]** | **Latest completion time** for node $v_j$（最迟完成时间） |
| **Slack Time** | 松弛时间 = LC - EC |
| **Critical Path** | Path consisting entirely of **zero-slack** edges（关键路径由松弛时间为 0 的边组成） |

#### 计算方法

**计算 EC（最早完成时间）**:
从 $v_0$ 开始，对于任意 $a_i = \langle v, w \rangle$：
$$EC[w] = \max\{EC[v] + cost(v, w) \mid \forall \langle v, w \rangle \in E\}$$

即：**正向计算，取最大值**（因为所有前驱活动都完成后才能开始）

**计算 LC（最迟完成时间）**:
从最后一个顶点 $v_{finish}$ 开始，对于任意 $a_i = \langle v, w \rangle$：
$$LC[v] = \min\{LC[w] - cost(v, w) \mid \forall \langle v, w \rangle \in E\}$$

即：**反向计算，取最小值**

**Slack Time（松弛时间）**  of $\langle v, w \rangle$：
$$Slack(v, w) = LC[w] - EC[v] - cost(v, w)$$

#### AOE 实例

假设一个项目的 AOE 网络：
- 顶点 $v_0$ 到 $v_8$，其中 $v_0$ = start，$v_8$ = finish
- 活动及其持续时间：
  - $a_0 = 6$（$v_0 \to v_1$），$a_1 = 4$（$v_0 \to v_2$），$a_2 = 5$（$v_0 \to v_3$）
  - $a_3 = 1$（$v_1 \to v_4$），$a_4 = 1$（$v_2 \to v_4$），$a_5 = 2$（$v_3 \to v_5$）
  - $a_6 = 9$（$v_4 \to v_6$），$a_7 = 7$（$v_4 \to v_7$），$a_8 = 4$（$v_5 \to v_7$）
  - $a_9 = 2$（$v_6 \to v_8$），$a_{10} = 4$（$v_7 \to v_8$）
  - $a_{11} = 0$（$v_6 \to v_7$ dummy activity，虚活动，不消耗时间）

**EC 计算（正向）** —— 从 $v_0$ 开始，对每条边 $\langle v, w \rangle$ 取最大值：

| Vertex | EC | 计算过程 |
|:------:|:--:|:--------:|
| v0 | 0 | 起点 |
| v1 | 6 | EC[0]+6=6 |
| v2 | 4 | EC[0]+4=4 |
| v3 | 5 | EC[0]+5=5 |
| v4 | 7 | max(EC[1]+1=7, EC[2]+1=5) = **7** |
| v5 | 7 | EC[3]+2=7 |
| v6 | 16 | EC[4]+9=16 |
| v7 | **16** | max(EC[4]+7=14, EC[5]+4=11, EC[6]+0=16) = **16** |
| v8 | **20** | max(EC[6]+2=18, EC[7]+4=20) = **20** |

注意：$v_7$ 的 EC 需考虑来自 $v_6$ 的虚活动 $a_{11}$（$EC[6]+0=16$），因此 $EC[7]=16$，而非 14。

**LC 计算（反向）** —— 从 $v_8$（终点）开始，对每条边 $\langle v, w \rangle$ 取最小值：
- 终点 $v_8$ 的 LC 等于其 EC（无松弛）

| Vertex | LC | 计算过程 |
|:------:|:--:|:--------:|
| v8 | **20** | LC[8] = EC[8] = 20 |
| v7 | **16** | LC[8] - a10 = 20 - 4 = 16 |
| v6 | **16** | min(LC[8]-a9=20-2=18, LC[7]-a11=16-0=16) = **16** |
| v5 | **12** | LC[7] - a8 = 16 - 4 = 12 |
| v4 | **7** | min(LC[6]-a6=16-9=7, LC[7]-a7=16-7=9) = **7** |
| v3 | **10** | LC[5] - a5 = 12 - 2 = 10 |
| v2 | **6** | LC[4] - a4 = 7 - 1 = 6 |
| v1 | **6** | LC[4] - a3 = 7 - 1 = 6 |
| v0 | **0** | min(LC[1]-a0=6-6=0, LC[2]-a1=6-4=2, LC[3]-a2=10-5=5) = **0** |

**松弛时间** $Slack(v, w) = LC[w] - EC[v] - cost(v, w)$:

| 活动 | 边 | Slack |
|:----:|:--:|:-----:|
| a0 | v0→v1 | 6-0-6 = **0** |
| a1 | v0→v2 | 6-0-4 = 2 |
| a2 | v0→v3 | 10-0-5 = 5 |
| a3 | v1→v4 | 7-6-1 = **0** |
| a4 | v2→v4 | 7-4-1 = 2 |
| a5 | v3→v5 | 12-5-2 = 5 |
| a6 | v4→v6 | 16-7-9 = **0** |
| a7 | v4→v7 | 16-7-7 = 2 |
| a8 | v5→v7 | 16-7-4 = 5 |
| a9 | v6→v8 | 20-16-2 = 2 |
| a10 | v7→v8 | 20-16-4 = **0** |
| a11 | v6→v7(虚) | 16-16-0 = **0** |

**Critical Path 关键路径**: 路径中所有边的 Slack = 0 的路径。本例中关键路径为：
$$v_0 \xrightarrow{a_0} v_1 \xrightarrow{a_3} v_4 \xrightarrow{a_6} v_6 \xrightarrow{a_{11}} v_7 \xrightarrow{a_{10}} v_8$$
项目总工期 = EC[8] = LC[8] = **20**。

---

### 3.6 All-Pairs Shortest Path Problem 所有点对最短路径

**问题**: For all pairs of $v_i$ and $v_j$ ($i \neq j$), find the shortest path between them.

**Method 1**: 对每个顶点运行单源最短路径算法，共 $|V|$ 次
- 若使用 Dijkstra: $T = O(|V|^3)$ — 适用于稀疏图
- 若使用无权 BFS: $T = O(|V| \times (|V| + |E|))$

**Method 2**: Floyd-Warshall 算法（$O(|V|^3)$）
- 在第 10 章中介绍
- 适用于稠密图

---

## §4 Network Flow Problems 网络流问题

### 4.1 问题定义

**示例**: Consider the following network of pipes（管道网络）

```
s --> a (2), s --> c (3)
a --> b (3), a --> d (1)
b --> t (2)
c --> a (2), c --> d (4)
d --> b (2), d --> t (3)
```

（括号内为容量 capacity）

**约束**: Total coming in ($v$) $\equiv$ Total going out ($v$), where $v \notin \{s, t\}$
（对于除源点和汇点外的每个顶点，流入量等于流出量）

**Goal**: Determine the **maximum amount of flow** that can pass from $s$ to $t$.（确定从源点到汇点能通过的最大流量）

### 4.2 简单算法 (A Simple Algorithm)

**基本步骤**:

1. **Step 1**: Find any path $s \to t$ in $G_r$ (residual graph) — 找一条**增广路径**
2. **Step 2**: Take the **minimum edge** on this path as the amount of flow and add to $G_f$ (flow graph) — 取路径上的最小边容量作为流量，加入流量图
3. **Step 3**: Update $G_r$ and remove the 0 flow edges — 更新**残余图**，移除流量为 0 的边
4. **Step 4**: If (there is a path $s \to t$ in $G_r$) Goto Step 1; Else End.

**残余图 $G_r$ 的更新**:
- 对于每条边 $(v, w)$，其**残余容量** = 原容量 - 当前流量
- 当一条边的流量等于其容量时（饱和），从残余图中移除

#### 贪心的问题

如果第一次选择路径 $s \to a \to d \to t$：
- 容量限制：$s \to a (2)$, $a \to d (1)$, $d \to t (3)$
- 流量 = min(2, 1, 3) = 1
- 更新后：
  - $s \to a$ 残余容量 = 1
  - $a \to d$ 残余容量 = 0（移除）
  - $d \to t$ 残余容量 = 2
- 问题：$a \to d$ 已饱和，可能无法达到最大流

> "It is simple indeed. But I bet that you will point out some problems here..."
> "Seems we cannot be greedy at this point."

**结论**: 简单的贪心策略不一定能得到最大流。

### 4.3 解决方案 — 允许算法撤销其决定

解决方法是：**允许算法撤销之前的决定**（allow the algorithm to undo its decisions）

**关键思想**:
For each edge $(v, w)$ with flow $f_{v,w}$ in $G_f$, add an **edge $(w, v)$ with flow $f_{v,w}$** in $G_r$.

在残余图中，对于流量图 $G_f$ 中每条有流量 $f_{v,w}$ 的边 $(v, w)$，添加一条**反向边** $(w, v)$，容量为 $f_{v,w}$。

这样，通过反向边可以"撤销"之前分配的流量。

**示例**:
原图 $G$:
- $s \to a (2)$，$s \to c (3)$
- $a \to b (3)$，$a \to d (1)$
- $b \to t (2)$
- $c \to a (2)$，$c \to d (4)$
- $d \to b (2)$，$d \to t (3)$

流量图 $G_f$ 和残余图 $G_r$ 在每一步后更新，增加了反向边。

**【Proposition】**: If the edge capabilities are rational numbers, this algorithm always terminate with a **maximum flow**.

如果边的容量是**有理数**，该算法总是以最大流终止。

**Note**: The algorithm works for $G$ with cycles as well.（该算法也适用于有环图）

### 4.4 算法分析

**假设**: capacities are all **integers**（容量均为整数）

**方法 1: 使用无权最短路径找增广路径**
- An augmenting path can be found by an **unweighted shortest path algorithm**（BFS）
- 每次增广至少增加 1 单位流量
- 复杂度: $T = O(f \cdot |E|)$，其中 $f$ 是最大流量

**最坏情况示例**:
```
s --1000000--> a --1000000--> t
s --1000000--> b --1000000--> t
s -> a (1), s -> b (1) 等等
```
如果每次增广只选择经过中间小容量边的路径，则需要 $f$ 次增广，导致 $O(f \cdot |E|)$ 复杂度。

**方法 2: 总是选择增加流量最大的增广路径**
- 修改 Dijkstra 算法来实现
- $T = T_{augmentation} \times T_{find\ a\ path} = O(|E| \log cap_{max}) \times O(|E| \log |V|)$
- 如果 $cap_{max}$ 是小整数，则 $T = O(|E|^2 \log |V|)$

**方法 3: 总是选择边数最少的增广路径**
- 使用无权最短路径算法
- $T = T_{augmentation} \times T_{find\ a\ path} = O(|E|) \times O(|E| \cdot |V|) = O(|E|^2 |V|)$
- **特殊情况**: 如果每个非源汇顶点都只有一条容量为 1 的入边或一条容量为 1 的出边，则时间可降低为 $O(|E| |V|^{1/2})$

### 4.5 Min-Cost Flow Problem 最小费用流

The **min-cost flow problem** is to find, among all maximum flows, the **one flow of minimum cost** provided that each edge has a **cost per unit of flow**.

在所有最大流中，找到总费用最小的流。每条边有一个单位流量费用。

---

## §5 Minimum Spanning Tree (MST) 最小生成树

### 5.1 定义

**【Definition】**: A **spanning tree** of a graph $G$ is a tree which consists of $V(G)$ and a subset of $E(G)$.

一个图 $G$ 的**生成树**是由 $V(G)$ 和 $E(G)$ 的一个子集组成的树。

**示例**: 一个完全图及其三个生成树（每个生成树都包含所有顶点，但边数 = $|V|-1$）

### 5.2 基本性质

1. **Tree**: The minimum spanning tree is a **tree** since it is **acyclic** — the number of edges is $|V| - 1$.
2. **Minimum**: It is minimum for the **total cost of edges** is minimized.
3. **Spanning**: It is spanning because it **covers every vertex**.
4. **Existence**: A minimum spanning tree exists **iff** $G$ is connected.
5. **Cycle Property**: Adding a **non-tree edge** to a spanning tree, we obtain a **cycle**.

### 5.3 贪心方法 (Greedy Method)

**约束条件**:
1. We must use only edges **within the graph**.
2. We must use **exactly** $|V| - 1$ edges.
3. We **may not use** edges that would produce a cycle.

### 5.4 Prim's Algorithm 普里姆算法

- **核心**: grow a tree（从一棵树开始生长）
- **非常类似于 Dijkstra 算法**（very similar to Dijkstra's algorithm）

**算法思路**:
1. 从任意一个顶点开始生成树
2. 每次选择一个**不在树中**且**距离树最近**的顶点加入树
3. 更新其他顶点到树的距离

**时间复杂度**:
- $T = O(|V|^2)$（类似 Dijkstra，使用简单扫描）
- $T = O(|E| \log |V|)$（使用优先队列）

**示例图**:
- $v_1 \to v_2 (2), v_4 (1)$
- $v_2 \to v_4 (3), v_5 (10)$
- $v_3 \to v_1 (4), v_6 (5)$
- $v_4 \to v_3 (2), v_5 (7), v_6 (8), v_7 (4)$
- $v_5 \to v_7 (6)$
- $v_6 \to v_7 (1)$

Prim 算法从 $v_1$ 开始，逐步选择最小权重的边连接新顶点到树中。

### 5.5 Kruskal's Algorithm 克鲁斯卡尔算法

- **核心**: maintain a forest（维护一个森林，逐步合并）

```c
void Kruskal( Graph G )
{
    T = { };
    while ( T contains less than |V| - 1 edges
            && E is not empty ) {
        choose a least cost edge (v, w) from E;
        delete (v, w) from E;
        if ( (v, w) does not create a cycle in T )
            add (v, w) to T;
        else
            discard (v, w);
    }
    if ( T contains fewer than |V| - 1 edges )
        Error( "No spanning tree" );
}
/* T = O( |E| log |E| ) */
```

**时间复杂度分析**:
- $T = O(|E| \log |E|)$
- **DeleteMin**: 使用优先队列选择最小边 → $O(\log|E|)$ per operation
- **Union/Find**: 检测是否会形成环 → 近乎 $O(1)$ per operation
- 更详细的伪代码见 Figure 9.58 on p.321

**Kruskal 算法步骤**（以示例图为例）:
1. 将所有边按权重排序
2. 从最小边开始，逐个加入不形成环的边
3. 直到有 $|V|-1$ 条边被选中

**Kruskal vs Prim**:

| Algorithm | Strategy | Data Structure | Time Complexity |
|-----------|----------|----------------|-----------------|
| Prim | Grow a tree | Priority Queue | $O(|E| \log\|V\|)$ |
| Kruskal | Maintain a forest | Union/Find + Priority Queue | $O(|E| \log\|E\|)$ |

---

## §6 Applications of Depth-First Search 深度优先搜索的应用

### 6.1 DFS 模板

```c
/* a generalization of preorder traversal */
void DFS( Vertex V )  /* this is only a template */
{
    visited[ V ] = true;  /* mark this vertex to avoid cycles */
    for ( each W adjacent to V )
        if ( !visited[ W ] )
            DFS( W );
}
/* T = O( |E| + |V| ) as long as adjacency lists are used */
```

- 这是**前序遍历**的推广
- 使用 `visited` 数组标记已访问顶点，避免环
- 如果使用邻接表，时间复杂度 $T = O(|E| + |V|)$

### 6.2 Undirected Graphs — ListComponents 列出连通分量

```c
void ListComponents( Graph G )
{
    for ( each V in G )
        if ( !visited[ V ] ) {
            DFS( V );
            printf("\n");
        }
}
```

**示例**:
给定图的结构：
- 0-1-4-6-5-2-3（连通分量 1）
- 7-8（连通分量 2）

执行过程：
- DFS(0): 访问 0, 1, 4, 6, 5, 2, 3
- DFS(7): 访问 7, 8

输出结果：`0 1 4 6 5 2 3`
         `7 8`

---

### 6.3 Biconnectivity 双连通性

#### 基本概念

- **Articulation Point (Cut Vertex) 关节点**: $v$ is an **articulation point** if $G' = DeleteVertex(G, v)$ has **at least 2 connected components**.
  - 删除顶点 $v$ 后，图分裂成至少两个连通分量。

- **Biconnected Graph 双连通图**: $G$ is **biconnected** if $G$ is connected and has **no articulation points**.

- **Biconnected Component 双连通分量**: a **maximal** biconnected subgraph.
  - **Note**: No edges can be **shared** by two or more biconnected components. Hence $E(G)$ is **partitioned** by the biconnected components of $G$.

#### 实例

**连通图**:
```
0 - 1 - 7 - 8 - 9
    |   |
    2   6
    |   |
    3 - 5
    |
    4
```

**双连通分量**:
- 分量 1: 0-1-2-3-4-5-6 (通过 1-7 和 7-6 连接)
- 分量 2: 7-8-9

**关节点**: 1, 7（删除后图会分裂）

#### 寻找双连通分量的算法

**Step 1**: 使用 DFS 获得图的**生成树**（Depth First Spanning Tree）

**Step 2**: 给每个顶点分配**深度优先编号**（Depth First Number, Num）

**Back Edge 回边**: $(u, v) \notin$ tree and $u$ (or $v$) is an **ancestor** of $v$ (or $u$).

**性质**: If $u$ is an ancestor of $v$, then $Num(u) < Num(v)$.

#### 寻找关节点 (Articulation Points)

**定义 Low 值**:
$$Low(v) = \min\{ Num(v), \min\{ Low(w) \mid w \text{ is a child of } v \}, \min\{ Num(w) \mid (v, w) \text{ is a back edge} \} \}$$

$Low(v)$ 是以下三者中的最小值：
1. $Num(v)$
2. 所有子节点 $w$ 的 $Low(w)$ 的最小值
3. 所有回边 $(v, w)$ 指向的 $w$ 的 $Num(w)$ 的最小值

**判定条件**:

1. **Root is an articulation point** iff it has **at least 2 children**.
   根节点是关节点当且仅当它**至少有 2 个子树**。

2. **Any other vertex $u$ is an articulation point** iff $u$ has **at least 1 child**, and it is **impossible to move down at least 1 step and then jump up to $u$'s ancestor**.
   即：$u$ 不是根节点，且存在至少一个子节点 $child$ 满足 $Low(child) \geq Num(u)$。

#### 实例追踪

**DFS 生成树**（从顶点 3 开始 DFS）：

DFS 顺序（Num）:
- Num[3] = 0, Num[4] = 1, Num[2] = 2, Num[1] = 3, Num[0] = 4
- Num[5] = 5, Num[6] = 6, Num[7] = 7, Num[9] = 8, Num[8] = 9

**Num 和 Low 表**:

| Vertex | Num | Low |
|:------:|:---:|:---:|
| 0 | 4 | 0 |
| 1 | 3 | 0 |
| 2 | 2 | 0 |
| 3 | 0 | 0 |
| 4 | 1 | 0 |
| 5 | 5 | 5 |
| 6 | 6 | 5 |
| 7 | 7 | 5 |
| 8 | 9 | 8 |
| 9 | 8 | 8 |

**DFS 生成树结构**（从顶点 3 开始 DFS）：

根节点 3 有两个子树：
- 子树 1: $3 \to 4 \to 2 \to 1 \to 0$
- 子树 2: $3 \to 5 \to 6 \to 7 \to 9 \to 8$

**关节点判定**（按规则检查每个顶点）：

| Vertex | Num | Low | 子节点 | Low(child) >= Num(u)? | 关节点? |
|:------:|:---:|:---:|:------:|:---------------------:|:-------:|
| 3(根) | 0 | 0 | 4, 5 (2 children) | 根节点至少有 2 个子树 | **是** |
| 4 | 1 | 0 | 2 | Low[2]=0 < Num[4]=1 | 否 |
| 2 | 2 | 0 | 1 | Low[1]=0 < Num[2]=2 | 否 |
| 1 | 3 | 0 | 0 | Low[0]=4 $\geq$ Num[1]=3 | **是** |
| 0 | 4 | 4 | 无子节点 | — | 否 |
| 5 | 5 | 5 | 6 | Low[6]=5 $\geq$ Num[5]=5 | **是** |
| 6 | 6 | 5 | 7 | Low[7]=5 < Num[6]=6 | 否 |
| 7 | 7 | 5 | 9 | Low[9]=8 $\geq$ Num[7]=7 | **是** |
| 9 | 8 | 8 | 8 | Low[8]=9 $\geq$ Num[9]=8 | **是** |
| 8 | 9 | 9 | 无子节点 | — | 否 |

**检查原则**：
- 根节点（3）：至少 2 个子树（4 和 5）→ 关节点
- 非根节点 $u$：存在至少一个子节点 $child$ 满足 $Low(child) \geq Num(u)$ → 关节点
  - $Low(child) \geq Num(u)$ 表示从 $child$ 及其子树出发，沿树边向下走再沿回边向上跳，**无法到达** $u$ 的祖先，因此删除 $u$ 会分离该子树
  - 等号（$=$）也成立，因为 $Low(child) = Num(u)$ 意味着 $child$ 子树能到达的最高顶点就是 $u$

**关键结论**:
$\quad$ $u$ is an articulation point iff
1. $u$ is the root and has at least 2 children; **or**
2. $u$ is not the root, and has at least 1 child such that $Low(child) \geq Num(u)$.

> **参考**: 详细伪代码见 p.327 和 p.329。

---

### 6.4 Euler Circuits 欧拉回路

#### 欧拉路径与欧拉回路

**Euler Tour (Eulerian Path) 欧拉路径**: Draw each line **exactly once** without lifting your pen from the paper.
（一笔画问题：每条边恰好画一次，笔不离开纸面）

**Euler Circuit (Eulerian Cycle) 欧拉回路**: Draw each line exactly once without lifting your pen from the paper, **AND finish at the starting point**.
（每条边恰好画一次，笔不离开纸面，**且回到起点**）

#### 判定条件

**【Proposition 1】**: An **Euler circuit** is possible **only if** the graph is **connected** and **each vertex has an even degree**.

欧拉回路存在的条件：图连通且**每个顶点的度数为偶数**。

**【Proposition 2】**: An **Euler tour** is possible **if there are exactly two vertices having odd degree**. One must start at **one of the odd-degree vertices**.

欧拉路径存在的条件：**恰好有两个顶点的度数为奇数**，且必须从其中一个奇数度顶点开始。

#### 寻找欧拉回路的算法（基于 DFS）

```
DFS 遍历图，同时跟踪：
1. 维护路径为链表（linked list）
2. 对每个邻接表，维护一个指针指向最后扫描的边
```

**执行示例**:
```
DFS 访问顺序: 1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 7 -> 8 -> 9 -> 10 -> 11 -> 12
```
（每个数字代表一个访问的顶点或边）

**时间复杂度**: $T = O(|E| + |V|)$

**实现要点**:
- The path should be maintained as a **linked list**.
- For each adjacency list, maintain a **pointer to the last edge scanned**.

#### Hamilton Cycle 哈密顿回路

Find a simple cycle in an undirected graph that **visits every vertex** — **Hamilton cycle**.

在无向图中找一个经过**每个顶点**恰好一次的简单环——**哈密顿回路**。

> 注意：哈密顿回路与欧拉回路的区别：
> - **欧拉回路**: 经过**每条边**恰好一次
> - **哈密顿回路**: 经过**每个顶点**恰好一次

---

### 6.5 补充学习资源

- 华为 iLearning 平台《搜索与人工智能 - 浙江大学专班》课程
- https://ilearningx.huawei.com/portal/courses/course-v1:HuaweiX+EBGTD9025+2021102902/about

---

## 附录：各算法复杂度总结

| Algorithm | Time Complexity | Space | Notes |
|-----------|----------------|-------|-------|
| **Topological Sort** (queue) | $O(\|V\| + \|E\|)$ | $O(\|V\|)$ | DAG only |
| **Unweighted Shortest Path** (queue) | $O(\|V\| + \|E\|)$ | $O(\|V\|)$ | BFS |
| **Dijkstra** (array) | $O(\|V\|^2 + \|E\|)$ | $O(\|V\|)$ | Dense graph |
| **Dijkstra** (priority queue) | $O(\|E\| \log\|V\|)$ | $O(\|V\| + \|E\|)$ | Sparse graph |
| **WeightedNegative** | $O(\|V\| \times \|E\|)$ | $O(\|V\|)$ | Detects negative cycles |
| **Acyclic Shortest Path** | $O(\|V\| + \|E\|)$ | $O(\|V\|)$ | Topological order |
| **All-Pairs** (Dijkstra x V) | $O(\|V\|^3)$ | $O(\|V\|^2)$ | Sparse graph |
| **All-Pairs** (Floyd) | $O(\|V\|^3)$ | $O(\|V\|^2)$ | Dense graph |
| **Network Flow** (basic) | $O(f \cdot \|E\|)$ | $O(\|E\|)$ | $f$ = max flow |
| **DFS** | $O(\|V\| + \|E\|)$ | $O(\|V\|)$ | Adjacency lists |
| **Prim** | $O(\|E\| \log\|V\|)$ | $O(\|V\|)$ | Similar to Dijkstra |
| **Kruskal** | $O(\|E\| \log\|E\|)$ | $O(\|V\| + \|E\|)$ | Union/Find |
| **Euler Circuit** | $O(\|V\| + \|E\|)$ | $O(\|V\| + \|E\|)$ | DFS-based |
| **Biconnectivity** | $O(\|V\| + \|E\|)$ | $O(\|V\|)$ | DFS + Low values |

---

## 附录：关键公式汇总

1. **Handshaking Theorem**: $\sum_{v \in V} degree(v) = 2|E|$

2. **Complete Graph Edges**:
   - Undirected: $|E| = n(n-1)/2$
   - Directed: $|E| = n(n-1)$

3. **Symmetric Matrix Storage**: $index(a_{ij}) = i(i-1)/2 + j$ (for $i \geq j$)

4. **Spanning Tree Edges**: $|E_{MST}| = |V| - 1$

5. **Dijkstra Update**: $distance'[u_2] = distance[u_1] + weight(\langle u_1, u_2 \rangle)$

6. **AOE EC**: $EC[w] = \max\{EC[v] + cost(v, w)\}$

7. **AOE LC**: $LC[v] = \min\{LC[w] - cost(v, w)\}$

8. **Slack Time**: $Slack(v, w) = LC[w] - EC[v] - cost(v, w)$

9. **Articulation Point Condition**: $Low(child) \geq Num(u)$

10. **Low Value**: $Low(v) = \min\{Num(v), \min Low(child), \min Num(back\:edge\:target)\}$
