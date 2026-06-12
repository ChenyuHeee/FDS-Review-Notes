## 2-4 Use Dijkstra algorithm to find the shortest paths from v1 to eve

- **类型**: MULTIPLE_CHOICE
- **分值**: 2 分
- **作者**: 何钦铭

---

When Dijkstra's algorithm is used to find the shortest paths from $$v_1$$ to every other vertices in an undirected graph G, a distance set $$dist[v]$$ is maintained for every vertex $$v$$, as the shortest distance from $$v_1$$ to $$v$$, passing through only the vertices whose shortest path to $$v_1$$ has been determined.

Suppose that the destinations are obtained in the order of { $$v_2$$, $$v_3$$, $$\cdots $$, $$v_n$$ }. If there is a vertex $$w$$ in G so that $$dist[w]$$ is decreased during every iteration (except the last one) of the algorithm, how many statements of the following are **correct**?

(1）$$w$$ is adjacent to every other vertices.

(2）($$v_1$$, $$w$$) (if it exists) is the longest edge among all the edges that are adjacent to $$w$$.

(3)  Such a vertex $$w$$ does not exist.
