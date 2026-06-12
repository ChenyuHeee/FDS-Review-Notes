## 5-1 Find Unweighted Shortest Paths

- **类型**: FILL_IN_THE_BLANK_FOR_PROGRAMMING
- **分值**: 6 分
- **作者**: 陈翔

---

The function `Unweighted` is to find the unweighted shortest path from `Vertex S` to every other vertices in a given `Graph`. The distances are stored in `dist[]`, and `path[]` records the paths. The `Graph` is defined as the following:

```
typedef struct GNode *PtrToGNode;
struct GNode{
    int Nv;          /* Number of vertices */
    int Ne;          /* Number of edges    */
    AdjList List;    /* adjacency matrix */
};
typedef PtrToGNode Graph;
```

```
void Unweighted( Graph G, Queue Q, int dist[], int path[], Vertex S )
{
  Vertex V, U;
  NodePtr ptr;

  dist[S] = 0;
  Enqueue(S, Q);
  while ( !IsEmpty(Q) ) {
    V = Dequeue( Q );
    for ( ptr=G->List[V].FirstEdge; ptr; ptr=ptr->Next) {
      U = ptr->AdjV;
      if ( dist[U] == INFINITY ) {
         @`[    ]`;
         path[U] = V;
         @`[    ]`;
      }
    }
  }
}
```
