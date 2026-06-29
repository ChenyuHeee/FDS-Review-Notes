# 错题整理 — 2026/06/29

## 1. BST: 4 和 6 同层，5 是否必为父节点?

**题目：** Given a binary search tree with 20 integer keys which include 4, 5, and 6, if 4 and 6 are on the same level, then 5 must be their parent.

**你的答案：** TRUE  
**正确答案：** FALSE

**解析：** 4 和 6 在同一层时，5 可以是它们的任意层级共同祖先，不一定是直接父节点。

**反例：**
```
        10
       /  \
      5    15
     / \   / \
    3   7 12  16
     \ /
     4 6
```
- 4 在第 3 层 (10→5→3→4)，父节点是 3
- 6 在第 3 层 (10→5→7→6)，父节点是 7
- 5 是它们的祖父，不是父节点

---

## 2. Connected Graph: E > V-1?

**题目：** In a connected graph, the number of edges must be greater than the number of vertices minus 1.

**你的答案：** FALSE（应该是 ≥，等号不能漏）  
**正确答案：** FALSE

**解析：** 树（tree）是连通无环图，恰好有 $V-1$ 条边。连通图的最小边数是 $V-1$，即 $E \geq V-1$。题目说 "greater than"（严格大于），漏掉了等于的情况，树就是反例。

---

