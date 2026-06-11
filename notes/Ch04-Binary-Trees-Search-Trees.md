# Chapter 4: Binary Trees and Search Trees 复习笔记

---

## §1 Preliminaries (预备知识)

### 1. Terminology (术语)

#### 树的定义 (Definition of a Tree)

**【Definition】** A **tree** is a collection of nodes. The collection can be empty; otherwise, a tree consists of:
1. A distinguished node **r**, called the **root** (根);
2. And zero or more nonempty **(sub)trees** $T_1, T_2, \dots, T_k$, each of whose roots are connected by a **directed edge** (有向边) from **r**.

> **Note:**
> - Subtrees must **not** connect together. Therefore every node in the tree is the root of some subtree.
> - There are **$N - 1$ edges** in a tree with $N$ nodes.
> - Normally the root is drawn at the **top**.

> **Diagram description:** The two classic examples introduced are:
> - **Pedigree Tree (族谱树)**: A binary tree representing family genealogy.
> - **Lineal Tree (谱系树)**: Shows ancestral lineage.

---

#### 基本术语 (Basic Terminology)

Let's use the following example tree for illustration:

```
        A
      / | \
     B  C  D
    / \    /|\
   E   F  H I J
  / \     |
 K   L    M
```

- **degree of a node (节点的度)**: number of subtrees of the node.
  - Example: degree(A) = 3, degree(F) = 0.
- **degree of a tree (树的度)**: the maximum degree among all nodes.
  - Example: degree of the tree above = 3.
- **parent (父节点)**: a node that has subtrees.
- **children (子节点)**: the roots of the subtrees of a parent.
- **siblings (兄弟节点)**: children of the same parent.
- **leaf / terminal node (叶子/终端节点)**: a node with degree 0 (no children).

---

#### 路径与度量 (Path and Measurements)

- **path from $n_1$ to $n_k$**: a (unique) sequence of nodes $n_1, n_2, \dots, n_k$ such that $n_i$ is the parent of $n_{i+1}$ for $1 \le i < k$.
- **length of path (路径长度)**: number of edges on the path.
- **depth of $n_i$ (深度)**: length of the unique path from the root to $n_i$.
  - Depth(root) = 0.
- **height of $n_i$ (高度)**: length of the longest path from $n_i$ to a leaf.
  - Height(leaf) = 0.
  - Example: height(D) = 2.
- **height (depth) of a tree (树的高度/深度)**: height(root) = depth(deepest leaf).
- **ancestors (祖先) of a node**: all the nodes along the path from the node up to the root.
- **descendants (后代) of a node**: all the nodes in its subtrees.

---

### 2. Implementation (树的实现)

#### List Representation (列表表示)

Represent a tree using nested lists:
- The root is the first element.
- Its subtrees follow as subsequent elements.

For the example tree:
```
(A)
(A (B, C, D))
(A (B (E, F), C (G), D (H, I, J)))
(A (B (E (K, L), F), C (G), D (H (M), I, J)))
```

> **Problem:** The size of each node depends on the number of branches -- that's not good for a fixed-size representation.

---

#### FirstChild-NextSibling Representation (左孩子右兄弟表示法)

Each node stores:
- **Element**: the data
- **FirstChild**: pointer to its first child
- **NextSibling**: pointer to its next sibling

```
Element | FirstChild | NextSibling
```

For the example tree structure:
```
A -> B -> C -> D -> NULL
|         |    |
E->F NULL G  H->I->J NULL
|           |     |
K->L NULL   NULL  M NULL
```

> **Note:** The representation is **not unique** since the children in a tree can be of any order.

---

## §2 Binary Trees (二叉树)

### 定义 (Definition)

**【Definition】** A **binary tree** is a tree in which **no node can have more than two children**.

> **Key difference from general trees:** In a tree, the order of children does not matter. But in a binary tree, **left child** and **right child** are distinct -- they are two different binary trees:
```
  A        A
 /    vs    \
B            B
```

#### Relationship with FirstChild-NextSibling

A binary tree can be obtained by **rotating the FirstChild-NextSibling tree clockwise by 45 degrees**:
- **Left** pointer = original FirstChild pointer
- **Right** pointer = original NextSibling pointer

```
        A                      A
       /                      / \
      B                      B   C
     / \                    / \   \
    E   C                  E   D   G
   / \   \                / \   \
  K   D   G              K   F   H
     / \                      \   \
    H   I                      L   I
     \                              \
      J                              J
```

After the 45-degree rotation, the tree structure is reinterpreted so that the first child becomes the **left child** and the next sibling becomes the **right child**, creating a strict binary tree representation.

---

### 二叉树的类型 (Types of Binary Trees)

- **Skewed Binary Trees (斜二叉树)**: All nodes have only left children or only right children.
  ```
  Skewed to the left:    Skewed to the right:
       A                     A
      /                       \
     B                         B
    /                           \
   C                             C
  /                               \
  D                                 D
  ```

- **Complete Binary Tree (完全二叉树)**: All leaf nodes are on two adjacent levels, and all nodes at the bottom level are as far left as possible.
  ```
        A
      /   \
     B     C
    / \   / \
   D   E F   G
  / \
  H  I
  ```

---

### 二叉树的性质 (Properties of Binary Trees)

1. **Maximum number of nodes on level $i$** is $2^{i-1}$, for $i \ge 1$.
   - Level 1: $2^{0} = 1$ node (the root)
   - Level 2: $2^{1} = 2$ nodes
   - Level $i$: $2^{i-1}$ nodes

2. **Maximum number of nodes in a binary tree of depth $k$** is $2^{k} - 1$, for $k \ge 1$.

3. **For any nonempty binary tree:**
   $$n_0 = n_2 + 1$$
   where $n_0$ is the number of **leaf nodes** (degree 0) and $n_2$ is the number of **nodes of degree 2**.

   **Proof:**
   - Let $n_1$ be the number of nodes of degree 1, and $n$ the total number of nodes.
   - Then $n = n_0 + n_1 + n_2$.
   - Let $B$ be the number of branches (edges). For a tree, $n = B + 1$.
   - All branches come from nodes of degree 1 or 2: $B = n_1 + 2n_2$.
   - Substituting: $n_0 + n_1 + n_2 = (n_1 + 2n_2) + 1$.
   - Therefore: $n_0 = n_2 + 1$.

---

### Expression Trees / Syntax Trees (表达式树/语法树)

An **expression tree** is a binary tree where:
- **Leaf nodes** are operands (e.g., A, B, C, D).
- **Internal nodes** are operators (e.g., +, *, /).

#### Example: Constructing an Expression Tree

Given infix expression: $A + B * C / D$

The expression tree:
```
      +
     / \
    A   /
       / \
      *   D
     / \
    B   C
```

#### Constructing from Postfix Expression

Given postfix expression: `a b + c d e + * *`

For expression: $(a + b) * (c * (d + e))$

Steps:
1. Read `a` -- push node(a)
2. Read `b` -- push node(b)
3. Read `+` -- pop b, pop a, create `+` node with left=a, right=b, push it
4. Read `c` -- push node(c)
5. Read `d` -- push node(d)
6. Read `e` -- push node(e)
7. Read `+` -- pop e, pop d, create `+` node with left=d, right=e, push
8. Read `*` -- pop (d+e), pop c, create `*` node with left=c, right=(d+e), push
9. Read `*` -- pop c*(d+e), pop (a+b), create `*` node with left=(a+b), right=c*(d+e)

Final tree:
```
        *
       / \
      +   *
     / \ / \
    a  b c  +
           / \
          d   e
```

---

### Tree Traversals (树的遍历)

Traversal means visiting **each node exactly once**.

#### Preorder Traversal (先序遍历)

```c
void preorder(tree_ptr tree) {
    if (tree) {
        visit(tree);
        for (each child C of tree)
            preorder(C);
    }
}
```

Order: **Root -> Left -> Right**
```
    1
   / \
  2   5
 / \   \
3   4   6
         \
          7
```
Preorder: 1, 2, 3, 4, 5, 6, 7

---

#### Postorder Traversal (后序遍历)

```c
void postorder(tree_ptr tree) {
    if (tree) {
        for (each child C of tree)
            postorder(C);
        visit(tree);
    }
}
```

Order: **Left -> Right -> Root**
```
    7
   / \
  3   6
 / \   \
1   2   5
         \
          4
```
Postorder: 1, 2, 3, 4, 5, 6, 7 (numbered by visit order)

Wait, let me re-check the diagram from the slides. The slide shows nodes numbered in visit order:

For postorder:
```
    7         (visited last)
   / \
  3   6
 / \   \
1   2   5
         \
          4
```
Postorder visit order: 1, 2, 3, 4, 5, 6, 7

---

#### Levelorder Traversal (层序遍历)

```c
void levelorder(tree_ptr tree) {
    enqueue(tree);
    while (queue is not empty) {
        visit(T = dequeue());
        for (each child C of T)
            enqueue(C);
    }
}
```

Order: visit level by level, from top to bottom, left to right.
```
    1
   / \
  2   3
 / \   \
4   5   6
         \
          7
```
Levelorder: 1, 2, 3, 4, 5, 6, 7

---

#### Inorder Traversal (中序遍历) -- for Binary Trees

**Recursive version:**
```c
void inorder(tree_ptr tree) {
    if (tree) {
        inorder(tree->Left);
        visit(tree->Element);
        inorder(tree->Right);
    }
}
```

**Iterative version (using stack):**
```c
void iter_inorder(tree_ptr tree) {
    Stack S = CreateStack(MAX_SIZE);
    for (;;) {
        for (; tree; tree = tree->Left)
            Push(tree, S);
        tree = Top(S);
        Pop(S);
        if (!tree) break;
        visit(tree->Element);
        tree = tree->Right;
    }
}
```

Order: **Left -> Root -> Right**

#### Example: Traversals of Expression Tree

Given infix expression: $A + B * C / D$

Expression tree:
```
      +
     / \
    A   /
       / \
      *   D
     / \
    B   C
```

- **Inorder traversal** $\Rightarrow$ $A + B * C / D$ (infix expression)
- **Postorder traversal** $\Rightarrow$ $A \; B \; C * D / +$ (postfix expression)
- **Preorder traversal** $\Rightarrow$ $+ \; A / * \; B \; C \; D$ (prefix expression)

---

### 实际应用示例 (Practical Examples)

#### Example: Directory Listing in a Hierarchical File System

Unix directory structure:
```
/usr
  |-- mark
  |     |-- book
  |     |     |-- ch1.c
  |     |     |-- ch2.c
  |     |     |-- ch3.c
  |     |-- course
  |     |     |-- cop3530
  |     |     |     |-- fall96 (syl.r)
  |     |     |     |-- spr97 (syl.r)
  |     |     |     |-- sum97 (syl.r)
  |     |-- hw.c
  |-- alex
  |     |-- hw.c
  |-- bill
        |-- work
        |-- course
              |-- cop3212
                    |-- fall96 (grades, p1.r, p2.r)
                    |-- fall97 (p2.r, p1.r, grades)
```

**Listing format:** Files at depth $d_i$ will have their names indented by $d_i$ tabs.

```c
static void ListDir(DirOrFile D, int Depth) {
    if (D is a legitimate entry) {
        PrintName(D, Depth);
        if (D is a directory)
            for (each child C of D)
                ListDir(C, Depth + 1);
    }
}
```
- $T(N) = O(N)$
- **Note:** `Depth` is an internal variable and must not be seen by the user. Solution: define an interface function:
```c
void ListDirectory(DirOrFile D) {
    ListDir(D, 0);
}
```

---

#### Example: Calculating the Size of a Directory

For the same directory structure, each file has a size. The size of a directory is the sum of all file sizes in its subtree.

```c
static int SizeDir(DirOrFile D) {
    int TotalSize;
    TotalSize = 0;
    if (D is a legitimate entry) {
        TotalSize = FileSize(D);
        if (D is a directory)
            for (each child C of D)
                TotalSize += SizeDir(C);
    }
    return TotalSize;
}
```
- $T(N) = O(N)$

---

### Threaded Binary Trees (线索二叉树)

#### Motivation

Consider a **full binary tree** with $n$ nodes:
- How many links are there? **$2n$** (each node has left and right pointers).
- How many of them are NULL? **$n + 1$** (in a binary tree with $n$ nodes, there are $n+1$ null links).

We can replace the **null links** by **"threads"** (线索), which will make traversals easier.

> Credits: **A. J. Perlis** and **C. Thornton**

#### Rules for Threaded Binary Trees

**Rule 1:** If `Tree->Left` is null, replace it with a pointer to the **inorder predecessor** of `Tree`.

**Rule 2:** If `Tree->Right` is null, replace it with a pointer to the **inorder successor** of `Tree`.

**Rule 3:** There must not be any loose threads. Therefore a threaded binary tree must have a **head node** (头节点) of which the left child points to the first node.

#### Data Structure

```c
typedef struct ThreadedTreeNode *PtrToThreadedNode;
typedef struct PtrToThreadedNode ThreadedTree;
typedef struct ThreadedTreeNode {
    int           LeftThread;   /* if TRUE, then Left is a thread, not a child ptr */
    ThreadedTree  Left;         /* left child or thread */
    ElementType   Element;
    int           RightThread;  /* if TRUE, then Right is a thread, not a child ptr */
    ThreadedTree  Right;        /* right child or thread */
};
```

- `LeftThread = TRUE`: `Left` is a thread (inorder predecessor)
- `LeftThread = FALSE`: `Left` is a child pointer
- `RightThread = TRUE`: `Right` is a thread (inorder successor)
- `RightThread = FALSE`: `Right` is a child pointer

#### Example: Threaded Binary Tree for $A + B * C / D$

Syntax tree (infix: $A + B * C / D$) threaded version:

```
             Head
            /    \
          ...    (points to first node)
         /
        +
       / \
      A   /
         / \
        *   D[RT=T -> Head]
       / \
      B   C
```

Thread details:
- Nodes with `F` (False) for the thread flag: actual child pointer
- Nodes with `T` (True) for the thread flag: thread pointer
- `A.Left` (thread) -> inorder predecessor (Head or NULL context)
- `A.Right` (thread) -> `+` (inorder successor of A)
- `C.Left` (thread) -> `*` (inorder predecessor of C)
- `C.Right` (thread) -> `/` (inorder successor of C)
- `D.Left` (thread) -> `/` (inorder predecessor of D)
- `D.Right` (thread) -> Head (inorder successor of D is the end of traversal)

These threads allow inorder traversal without using a stack.

---

## §3 The Search Tree ADT -- Binary Search Trees (二叉搜索树)

### 1. Definition (定义)

**【Definition】** A **binary search tree (BST)** is a binary tree. It may be empty. If it is not empty, it satisfies the following properties:

1. Every node has a **key** which is an integer, and the keys are **distinct** (互不相同).
2. The keys in a **nonempty left subtree** must be **smaller** than the key in the root of the subtree.
3. The keys in a **nonempty right subtree** must be **larger** than the key in the root of the subtree.
4. The left and right subtrees are also **binary search trees**.

**Examples:**
```
Valid BST:                    Invalid (not BST):
      30                          20
    /    \                      /    \
   5     40                   10      30
  / \      \                 /  \    /  \
 2  15     60              15   5  25   35
    / \   / \
   12 25 50 70
         /
        65
```

---

### 2. ADT (抽象数据类型)

**Objects:** A finite ordered list with zero or more elements.

**Operations:**
| Operation | Signature | Description |
|-----------|-----------|-------------|
| `MakeEmpty` | `SearchTree MakeEmpty(SearchTree T)` | Empty the tree |
| `Find` | `Position Find(ElementType X, SearchTree T)` | Find element X |
| `FindMin` | `Position FindMin(SearchTree T)` | Find minimum element |
| `FindMax` | `Position FindMax(SearchTree T)` | Find maximum element |
| `Insert` | `SearchTree Insert(ElementType X, SearchTree T)` | Insert element X |
| `Delete` | `SearchTree Delete(ElementType X, SearchTree T)` | Delete element X |
| `Retrieve` | `ElementType Retrieve(Position P)` | Retrieve element at position P |

---

### 3. Implementations (实现)

#### Find (查找)

**Recursive version:**
```c
Position Find(ElementType X, SearchTree T) {
    if (T == NULL)
        return NULL;           /* not found in an empty tree */
    if (X < T->Element)        /* if smaller than root */
        return Find(X, T->Left);   /* search left subtree */
    else if (X > T->Element)   /* if larger than root */
        return Find(X, T->Right);  /* search right subtree */
    else                       /* if X == root */
        return T;              /* found */
}
```

> These are **tail recursions** (尾递归) -- easily converted to iteration.
>
> $T(N) = S(N) = O(d)$ where $d$ is the depth of $X$.

**Iterative version:**
```c
Position Iter_Find(ElementType X, SearchTree T) {
    /* iterative version of Find */
    while (T) {
        if (X == T->Element)
            return T;          /* found */
        if (X < T->Element)
            T = T->Left;       /* move down along left path */
        else
            T = T->Right;      /* move down along right path */
    }
    return NULL;               /* not found */
}
```

---

#### FindMin (找最小值)

**Recursive version:**
```c
Position FindMin(SearchTree T) {
    if (T == NULL)
        return NULL;               /* not found in an empty tree */
    else if (T->Left == NULL)
        return T;                  /* found left most */
    else
        return FindMin(T->Left);   /* keep moving to left */
}
```
$T(N) = O(d)$

---

#### FindMax (找最大值)

**Iterative version:**
```c
Position FindMax(SearchTree T) {
    if (T != NULL)
        while (T->Right != NULL)
            T = T->Right;          /* keep moving to find right most */
    return T;                      /* return NULL or the right most */
}
```
$T(N) = O(d)$

---

#### Insert (插入)

**Sketch of the idea:**

Starting BST:
```
      30
     /  \
    5    40
   /
  2
```

- **Insert 80:**
  1. Check if 80 is already in the tree.
  2. 80 > 30, go right; 80 > 40, go right -> NULL.
  3. Insert 80 as the right child of 40.

- **Insert 35:**
  1. Check if 35 is already in the tree.
  2. 35 > 30, go right; 35 < 40, go left -> NULL.
  3. Insert 35 as the left child of 40.

- **Insert 25:**
  1. Check if 25 is already in the tree.
  2. 25 < 30, go left; 25 > 5, go right -> NULL.
  3. Insert 25 as the right child of 5.

> The last node we encounter when searching for the key number will be the **parent** of the new node.

**Implementation:**
```c
SearchTree Insert(ElementType X, SearchTree T) {
    if (T == NULL) {
        /* Create and return a one-node tree */
        T = malloc(sizeof(struct TreeNode));
        if (T == NULL)
            FatalError("Out of space!!!");
        else {
            T->Element = X;
            T->Left = T->Right = NULL;
        }
    }
    /* If there is a tree */
    else if (X < T->Element)
        T->Left = Insert(X, T->Left);
    else if (X > T->Element)
        T->Right = Insert(X, T->Right);
    /* Else X is in the tree already; we'll do nothing */

    return T;   /* Do not forget this line!! */
}
```
$T(N) = O(d)$

> **Question:** How would you handle duplicated keys?
> - **Answer:** One approach is to add a counter field in each node, or store them in the left/right subtree consistently.

---

#### Delete (删除)

Three cases based on the degree of the node to be deleted:

##### Case 1: Delete a leaf node (度为0)
**Reset its parent link to NULL** and free the node.

##### Case 2: Delete a degree 1 node (度为1)
**Replace the node by its single child.**

```
Before:          After:
   P               P
    \               \
     X               C
    /
   C
```

##### Case 3: Delete a degree 2 node (度为2)
1. **Replace the node's element** by the largest element in its **left subtree** (the rightmost node of the left subtree), or the **smallest** element in its **right subtree** (the leftmost node of the right subtree).
2. **Delete the replacing node** from the subtree (which will be either Case 1 or Case 2).

**Example: Delete 60 from BST**
```
        40
       /  \
      20   60
     / \   / \
    10 30 50 70
         / \
        45 55
           /
          52
```

- **Solution 1 (use left subtree):** Replace 60 with 55 (largest in left subtree), then delete 55 from left subtree.
- **Solution 2 (use right subtree):** Replace 60 with the smallest element in its right subtree (which is 70), then delete the original 70 from the right subtree.

More detailed example with the tree:
```
      40
     /  \
   20    60
  /  \   / \
 10  30 50 70
        \
        55
        /
       52
```

Delete 60:
- Find the smallest in the right subtree of 60: FindMin(60's right subtree = 70) -> 70 (the leftmost node).
- Replace 60's element with 70.
- Delete the original 70 from the right subtree.

OR:
- Find the largest in the left subtree of 60: FindMax(60's left subtree = 50's right) -> 55.
- Replace 60's element with 55.
- Delete 55 from the left subtree (which is a degree 1 node, so replace it with 52).

**Implementation:**
```c
SearchTree Delete(ElementType X, SearchTree T) {
    Position TmpCell;

    if (T == NULL)
        Error("Element not found");
    else if (X < T->Element)        /* Go left */
        T->Left = Delete(X, T->Left);
    else if (X > T->Element)        /* Go right */
        T->Right = Delete(X, T->Right);
    else {                          /* Found element to be deleted */
        if (T->Left && T->Right) {  /* Two children */
            /* Replace with smallest in right subtree */
            TmpCell = FindMin(T->Right);
            T->Element = TmpCell->Element;
            T->Right = Delete(T->Element, T->Right);
        }
        else {                      /* One or zero child */
            TmpCell = T;
            if (T->Left == NULL)    /* Also handles 0 child */
                T = T->Right;
            else if (T->Right == NULL)
                T = T->Left;
            free(TmpCell);
        }
    }
    return T;
}
```
$T(N) = O(h)$ where $h$ is the height of the tree.

---

### Lazy Deletion (懒惰删除)

**Idea:** If there are not many deletions, add a **flag field** to each node, to mark if a node is **active** or is **deleted**.

- We can "delete" a node without actually freeing the space of that node.
- If a deleted key is reinserted, we won't have to call `malloc` again.

**Trade-off:** If the number of deleted nodes is the same as the number of active nodes, the tree's height may increase, potentially affecting efficiency of operations (since the tree can become filled with "dead" nodes).

---

### 4. Average-Case Analysis (平均情况分析)

**Question:** Place $n$ elements in a binary search tree. How high can this tree be?

**Answer:** The height depends on the **order of insertion**.

#### Example: Inserting 1, 2, 3, 4, 5, 6, 7

**Order 1:** `4, 2, 1, 3, 6, 5, 7`
```
        4
      /   \
     2     6
    / \   / \
   1   3 5   7
```
Height $h = 2$ (best case -- balanced tree)

**Order 2:** `1, 2, 3, 4, 5, 6, 7`
```
1
 \
  2
   \
    3
     \
      4
       \
        5
         \
          6
           \
            7
```
Height $h = 6$ (worst case -- skewed tree)

#### Key Insight:
- **Average depth** of a BST node is $O(\log N)$ if insertions are random.
- **Worst-case depth** is $O(N)$ (when elements are inserted in sorted/nearly-sorted order).
- The expected height of a randomly built BST is $O(\log N)$.

---

## Summary of Key Formulas

| Property | Formula |
|----------|---------|
| Max nodes on level $i$ | $2^{i-1}$ |
| Max nodes in tree of depth $k$ | $2^k - 1$ |
| Leaf nodes vs degree-2 nodes | $n_0 = n_2 + 1$ |
| Edges in tree with $N$ nodes | $N - 1$ |
| Null links in binary tree with $n$ nodes | $n + 1$ |
| BST Find/Insert time (average) | $O(\log N)$ |
| BST Find/Insert time (worst case) | $O(N)$ |

## Summary of Traversal Orders

| Traversal | Order | Visit Pattern |
|-----------|-------|---------------|
| Preorder | Root -> Left -> Right | Visit before children |
| Inorder | Left -> Root -> Right | Visit between children |
| Postorder | Left -> Right -> Root | Visit after children |
| Levelorder | Top level to bottom, left to right | BFS using queue |
