# FDS 课程复习笔记

> 笔记采用中文解释 + 英文术语对照格式，兼顾理解与考试。

## 章节索引

| 章节 | 内容 | 英文标题 |
|------|------|----------|
| [第2章](Ch02-Algorithm-Analysis.md) | 算法分析 | Algorithm Analysis |
| [第3章](Ch03-List-Stack-Queue.md) | 链表、栈与队列 | Lists, Stacks, and Queues |
| [第4章](Ch04-Binary-Trees-Search-Trees.md) | 二叉树与搜索树 | Binary Trees & Search Trees |
| [第5章](Ch05-Priority-Queues.md) | 优先队列 | Priority Queues |
| [第6章](Ch06-Sorting-Algorithms.md) | 排序算法 | Sorting Algorithms |
| [第7章](Ch07-Hashing.md) | 哈希 | Hashing |
| [第8章](Ch08-Union-Find-Segment-Tree.md) | 并查集与线段树 | Union-Find & Segment Tree |
| [第9章](Ch09-Graph-Algorithms.md) | 图算法 | Graph Algorithms |

## 知识体系速览

### 数据结构
- **线性结构**：链表（单/双/循环）→ 栈（LIFO）→ 队列（FIFO）
- **树结构**：二叉树 → 二叉搜索树（BST）→ 线索二叉树 → 堆 → d-堆
- **图结构**：邻接矩阵/邻接表 → 有向/无向图 → DAG → AOV/AOE 网络
- **其他**：哈希表、并查集、线段树

### 算法
- **排序**：插入排序 → 希尔排序 → 堆排序 → 归并排序 → 快速排序 → 基数排序
- **图算法**：拓扑排序 → 最短路径（Dijkstra / Bellman-Ford / Floyd）→ 网络流 → MST（Prim / Kruskal）→ DFS 应用（双连通性 / 欧拉回路）
- **查找**：二分查找 → BST 查找 → 哈希查找
- **其他**：分治法、贪心、动态等价问题、路径压缩

### 复杂度层次

| 复杂度 | 代表算法 |
|--------|----------|
| $O(1)$ | 数组索引、哈希查找（平均） |
| $O(\log N)$ | 二分查找、堆操作、BST 查找（平均） |
| $O(N)$ | 线性扫描、在线最大子序列和、BuildHeap |
| $O(N \log N)$ | 归并排序、快速排序（平均）、堆排序 |
| $O(N^2)$ | 插入排序、冒泡排序、朴素 Dijkstra |
| $O(N^3)$ | Floyd-Warshall、矩阵乘法 |
| $O(2^N)$ | 朴素斐波那契递归 |

## 使用方式

1. 按章节顺序复习，每天 1-2 章
2. 阅读中文理解概念，关注括号内的英文术语（考试用英文）
3. 手动推演书上的例题和算法 trace
4. 遇到问题随时向 Claude 提问

> 笔记基于 2026 年春夏学期课件整理，经四轮处理（生成 → 校对 → 中文化 → 术语补充），可直接用于期末复习。
