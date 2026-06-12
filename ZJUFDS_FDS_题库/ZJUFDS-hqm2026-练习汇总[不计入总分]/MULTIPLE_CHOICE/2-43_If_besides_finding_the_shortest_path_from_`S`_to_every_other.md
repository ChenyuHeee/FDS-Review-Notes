## 2-43 If besides finding the shortest path from `S` to every other ver

- **类型**: MULTIPLE_CHOICE
- **分值**: 2 分
- **作者**: DS课程组

---

If besides finding the shortest path from `S` to every other vertices, we also need to count the number of different shortest paths, we can modify the Dijkstra algorithm in the following way: add an array `count[]` so that `count[V]` records the number of different shortest paths from `S` to `V`.  Then `count[V]` shall be initialized as: `[    ]`

A. `count[S]=1; ` and `count[V]=0` for other `V`
B. `count[S]=0; ` and `count[V]=1` for other `V`
C. `count[V]=1` for all vertices
D. `count[V]=0` for all vertices
