# 第4章：二叉树与搜索树 复习笔记 (Chapter 4: Binary Trees & Search Trees)

---

## §1 预备知识 (Preliminaries)

### 1. 术语 (Terminology)

#### 树的定义 (Tree Definition)

**【定义】** **树（tree）** 是一个节点的集合。该集合可以为空；否则，一棵树由以下部分组成：
1. 一个特殊的节点 **r**，称为 **根（root）**；
2. 以及零个或多个非空的**（子）树** $T_1, T_2, \dots, T_k$，每棵子树的根通过一条从 **r** 出发的**有向边（directed edge）** 相连。

> **注意：**
> - 子树之间**不能**互相连接。因此，树中的每个节点都是某棵子树的根。
> - 一棵具有 $N$ 个节点的树有 **$N - 1$ 条边**。
> - 通常根被绘制在**顶部**。

> **图示说明：** 介绍了两个经典例子：
> - **族谱树 (Pedigree Tree)**：表示家族谱系的二叉树。
> - **谱系树 (Lineal Tree)**：显示祖先世系。

---

#### 基本术语 (Basic Terminology)

以下面的示例树进行说明：

```
        A
      / | \
     B  C  D
    / \    /|\
   E   F  H I J
  / \     |
 K   L    M
```

- **节点的度 (degree of a node)**：节点拥有的子树个数。
  - 例如：degree(A) = 3，degree(F) = 0。
- **树的度 (degree of a tree)**：所有节点中最大的度。
  - 例如：上面这棵树的度 = 3。
- **父节点 (parent)**：拥有子树的节点。
- **子节点 (children)**：父节点的子树的根。
- **兄弟节点 (siblings)**：具有相同父节点的子节点。
- **叶子节点/终端节点 (leaf / terminal node)**：度为 0（没有子节点）的节点。

---

#### 路径与度量 (Path and Measurement)

- **从 $n_1$ 到 $n_k$ 的路径（path）**：一个（唯一的）节点序列 $n_1, n_2, \dots, n_k$，其中对于 $1 \le i < k$，$n_i$ 是 $n_{i+1}$ 的父节点。
- **路径长度 (length of path)**：路径上的边的数量。
- **$n_i$ 的深度 (depth)**：从根到 $n_i$ 的唯一路径的长度。
  - Depth(root) = 0。
- **$n_i$ 的高度 (height)**：从 $n_i$ 到某个叶子节点的最长路径的长度。
  - Height(leaf) = 0。
  - 例如：height(D) = 2。
- **树的高度/深度 (height/depth of a tree)**：height(root) = depth(最深的叶子节点)。
- **节点的祖先 (ancestors)**：从该节点向上到根节点的路径上的所有节点。
- **节点的后代 (descendants)**：其子树中的所有节点。

---

### 2. 树的实现 (Implementation)

#### 列表表示 (List Representation)

使用嵌套列表表示树：
- 第一个元素为根。
- 其后的元素为子树。

对于示例树：
```
(A)
(A (B, C, D))
(A (B (E, F), C (G), D (H, I, J)))
(A (B (E (K, L), F), C (G), D (H (M), I, J)))
```

> **问题：** 每个节点的大小取决于分支的数量——这对于固定大小的表示方式来说是不利的。

---

#### 左孩子右兄弟表示法 (FirstChild-NextSibling Representation)

每个节点存储：
- **Element**：数据
- **FirstChild**：指向第一个孩子的指针
- **NextSibling**：指向下一个兄弟的指针

```
Element | FirstChild | NextSibling
```

对于示例树的结构：
```
A -> B -> C -> D -> NULL
|         |    |
E->F NULL G  H->I->J NULL
|           |     |
K->L NULL   NULL  M NULL
```

> **注意：** 由于树中孩子的顺序可以是任意的，因此这种表示方式**不唯一**。

---

## §2 二叉树 (Binary Trees)

### 定义 (Definition)

**【定义】** **二叉树（binary tree）** 是一棵树，其中**每个节点的孩子数不能超过两个**。

> **与一般树的关键区别：** 在树中，孩子的顺序无关紧要。但在二叉树中，**左孩子**和**右孩子**是有区别的——它们是两种不同的二叉树：
```
  A        A
 /    vs    \
B            B
```

#### 与左孩子右兄弟表示法的关系

将左孩子右兄弟树**顺时针旋转 45 度**即可得到一棵二叉树：
- **Left** 指针 = 原来的 FirstChild 指针
- **Right** 指针 = 原来的 NextSibling 指针

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

经过 45 度旋转后，树的结构被重新解释：第一个孩子变为**左孩子**，下一个兄弟变为**右孩子**，从而形成严格的二叉树表示。

---

### 二叉树的类型 (Types of Binary Trees)

- **斜二叉树 (Skewed Binary Trees)**：所有节点只有左孩子或只有右孩子。
  ```
  向左倾斜：            向右倾斜：
       A                     A
      /                       \
     B                         B
    /                           \
   C                             C
  /                               \
  D                                 D
  ```

- **完全二叉树 (Complete Binary Tree)**：所有叶子节点位于两个相邻的层上，且底层所有节点都尽可能靠左排列。
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

1. **第 $i$ 层上最多节点数**为 $2^{i-1}$，其中 $i \ge 1$。
   - 第 1 层：$2^{0} = 1$ 个节点（根）
   - 第 2 层：$2^{1} = 2$ 个节点
   - 第 $i$ 层：$2^{i-1}$ 个节点

2. **深度为 $k$ 的二叉树中最多节点数**为 $2^{k} - 1$，其中 $k \ge 1$。

3. **对于任何非空二叉树：**
   $$n_0 = n_2 + 1$$
   其中 $n_0$ 是**叶子节点**（度为 0）的数量，$n_2$ 是**度为 2 的节点**的数量。

   **证明：**
   - 设 $n_1$ 为度为 1 的节点数，$n$ 为总节点数。
   - 则 $n = n_0 + n_1 + n_2$。
   - 设 $B$ 为分支（边）的数量。对于树，$n = B + 1$。
   - 所有分支来自度为 1 或 2 的节点：$B = n_1 + 2n_2$。
   - 代入：$n_0 + n_1 + n_2 = (n_1 + 2n_2) + 1$。
   - 因此：$n_0 = n_2 + 1$。

---

### 表达式树/语法树 (Expression Trees / Syntax Trees)

**表达式树（expression tree）** 是一种二叉树，其中：
- **叶子节点**为操作数 (operands)（例如 A, B, C, D）。
- **内部节点 (internal nodes)** 为运算符 (operators)（例如 +, *, /）。

#### 示例：构造表达式树 (Example: Building an Expression Tree)

给定中缀表达式 (infix expression)：$A + B * C / D$

表达式树：
```
      +
     / \
    A   /
       / \
      *   D
     / \
    B   C
```

#### 从后缀表达式构造 (Constructing from Postfix Expression)

给定后缀表达式 (postfix expression)：`a b + c d e + * *`

对应表达式：$(a + b) * (c * (d + e))$

步骤：
1. 读取 `a` —— 压入 node(a)
2. 读取 `b` —— 压入 node(b)
3. 读取 `+` —— 弹出 b，弹出 a，创建 `+` 节点（左=a，右=b），压入
4. 读取 `c` —— 压入 node(c)
5. 读取 `d` —— 压入 node(d)
6. 读取 `e` —— 压入 node(e)
7. 读取 `+` —— 弹出 e，弹出 d，创建 `+` 节点（左=d，右=e），压入
8. 读取 `*` —— 弹出 (d+e)，弹出 c，创建 `*` 节点（左=c，右=(d+e)），压入
9. 读取 `*` —— 弹出 c*(d+e)，弹出 (a+b)，创建 `*` 节点（左=(a+b)，右=c*(d+e)）

最终树：
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

### 树的遍历 (Tree Traversals)

遍历是指**恰好访问每个节点一次**。

#### 先序遍历 (Preorder Traversal)

```c
void preorder(tree_ptr tree) {
    if (tree) {
        visit(tree);
        for (each child C of tree)
            preorder(C);
    }
}
```

顺序：**根 -> 左 -> 右**
```
    1
   / \
  2   5
 / \   \
3   4   6
         \
          7
```
先序遍历结果：1, 2, 3, 4, 5, 6, 7

---

#### 后序遍历 (Postorder Traversal)

```c
void postorder(tree_ptr tree) {
    if (tree) {
        for (each child C of tree)
            postorder(C);
        visit(tree);
    }
}
```

顺序：**左 -> 右 -> 根**
```
    7
   / \
  3   6
 / \   \
1   2   5
         \
          4
```
后序遍历结果：1, 2, 3, 4, 5, 6, 7（按访问顺序编号）

幻灯片中的图示，节点按访问顺序编号：

后序遍历：
```
    7         （最后访问）
   / \
  3   6
 / \   \
1   2   5
         \
          4
```
后序访问顺序：1, 2, 3, 4, 5, 6, 7

---

#### 层序遍历 (Levelorder Traversal)

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

顺序：逐层访问，从上到下，从左到右。
```
    1
   / \
  2   3
 / \   \
4   5   6
         \
          7
```
层序遍历结果：1, 2, 3, 4, 5, 6, 7

---

#### 中序遍历 (Inorder Traversal) —— 针对二叉树

**递归版本 (Recursive Version)：**
```c
void inorder(tree_ptr tree) {
    if (tree) {
        inorder(tree->Left);
        visit(tree->Element);
        inorder(tree->Right);
    }
}
```

**迭代版本 (Iterative Version)（使用栈, stack）：**
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

顺序：**左 -> 根 -> 右**

#### 示例：表达式树的遍历 (Example: Traversing an Expression Tree)

给定中缀表达式 (infix expression)：$A + B * C / D$

表达式树：
```
      +
     / \
    A   /
       / \
      *   D
     / \
    B   C
```

- **中序遍历** $\Rightarrow$ $A + B * C / D$（中缀表达式, infix expression）
- **后序遍历** $\Rightarrow$ $A \; B \; C * D / +$（后缀表达式, postfix expression）
- **先序遍历** $\Rightarrow$ $+ \; A / * \; B \; C \; D$（前缀表达式, prefix expression）

---

### 实际应用示例 (Practical Examples)

#### 示例：层次化文件系统中的目录列表 (Example: Directory Listing in a Hierarchical File System)

Unix 目录结构：
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

**列出格式：** 深度为 $d_i$ 的文件，其名称缩进 $d_i$ 个制表符。

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
- **注意：** `Depth` 是一个内部变量，不能暴露给用户。解决方法：定义一个接口函数：
```c
void ListDirectory(DirOrFile D) {
    ListDir(D, 0);
}
```

---

#### 示例：计算目录大小 (Example: Computing Directory Size)

对于同样的目录结构，每个文件都有大小。目录的大小是其子树中所有文件大小的总和。

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

### 线索二叉树 (Threaded Binary Trees)

#### 动机 (Motivation)

考虑一棵具有 $n$ 个节点的**满二叉树 (Full Binary Tree)**：
- 共有多少条链接？**$2n$**（每个节点有左、右两个指针）。
- 其中有多少是 NULL？**$n + 1$**（在具有 $n$ 个节点的二叉树中，有 $n+1$ 个空链接 (null links)）。

我们可以用**"线索"（threads）** 替代这些**空链接**，这将使遍历变得更加容易。

> 提出者：**A. J. Perlis** 和 **C. Thornton**

#### 线索二叉树的规则 (Rules for Threaded Binary Trees)

**规则 1 (Rule 1)：** 如果 `Tree->Left` 为空，则将其替换为指向 `Tree` 的**中序前驱 (inorder predecessor)** 的指针。

**规则 2 (Rule 2)：** 如果 `Tree->Right` 为空，则将其替换为指向 `Tree` 的**中序后继 (inorder successor)** 的指针。

**规则 3 (Rule 3)：** 不能有"悬空 (dangling)"的线索。因此，线索二叉树必须有一个**头节点（head node）**，其左孩子指向第一个节点。

#### 数据结构

```c
typedef struct ThreadedTreeNode *PtrToThreadedNode;
typedef struct PtrToThreadedNode ThreadedTree;
typedef struct ThreadedTreeNode {
    int           LeftThread;   /* 若为 TRUE，则 Left 是线索，而非孩子指针 */
    ThreadedTree  Left;         /* 左孩子或线索 */
    ElementType   Element;
    int           RightThread;  /* 若为 TRUE，则 Right 是线索，而非孩子指针 */
    ThreadedTree  Right;        /* 右孩子或线索 */
};
```

- `LeftThread = TRUE`：`Left` 是线索（中序前驱）
- `LeftThread = FALSE`：`Left` 是孩子指针
- `RightThread = TRUE`：`Right` 是线索（中序后继）
- `RightThread = FALSE`：`Right` 是孩子指针

#### 示例：$A + B * C / D$ 的线索二叉树 (Example: Threaded Binary Tree of $A + B * C / D$)

语法树（中缀：$A + B * C / D$）的线索化版本：

```
             Head
            /    \
          ...    (指向第一个节点)
         /
        +
       / \
      A   /
         / \
        *   D[RT=T -> Head]
       / \
      B   C
```

线索细节：
- 线程标志为 `F`（False）的节点：实际的孩子指针
- 线程标志为 `T`（True）的节点：线索指针
- `A.Left`（线索）-> 中序前驱（Head 或 NULL 上下文）
- `A.Right`（线索）-> `+`（A 的中序后继）
- `C.Left`（线索）-> `*`（C 的中序前驱）
- `C.Right`（线索）-> `/`（C 的中序后继）
- `D.Left`（线索）-> `/`（D 的中序前驱）
- `D.Right`（线索）-> Head（D 的中序后继即遍历的终点）

这些线索使得无需使用栈即可进行中序遍历。

---

## §3 搜索树 ADT——二叉搜索树 (Binary Search Trees)

### 1. 定义 (Definition)

**【定义】** **二叉搜索树（binary search tree，BST）** 是一种二叉树。它可以为空。若非空，则满足以下性质：

1. 每个节点都有一个**关键字（key）**，关键字为整数，且**互不相同（distinct）**。
2. **非空左子树 (non-empty left subtree)** 中的所有关键字**小于**子树根节点的关键字。
3. **非空右子树 (non-empty right subtree)** 中的所有关键字**大于**子树根节点的关键字。
4. 左子树和右子树也都是**二叉搜索树**。

**示例：**
```
有效的 BST：                    无效的（不是 BST）：
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

### 2. ADT（抽象数据类型，Abstract Data Type）

**对象 (Object)：** 一个包含零个或多个元素的有序列表。

**操作：**
| 操作 | 签名 | 说明 |
|-----------|-----------|-------------|
| `MakeEmpty` | `SearchTree MakeEmpty(SearchTree T)` | 清空树 |
| `Find` | `Position Find(ElementType X, SearchTree T)` | 查找元素 X |
| `FindMin` | `Position FindMin(SearchTree T)` | 查找最小元素 |
| `FindMax` | `Position FindMax(SearchTree T)` | 查找最大元素 |
| `Insert` | `SearchTree Insert(ElementType X, SearchTree T)` | 插入元素 X |
| `Delete` | `SearchTree Delete(ElementType X, SearchTree T)` | 删除元素 X |
| `Retrieve` | `ElementType Retrieve(Position P)` | 获取位置 P 处的元素 |

---

### 3. 实现 (Implementations)

#### 查找 (Find)

**递归版本：**
```c
Position Find(ElementType X, SearchTree T) {
    if (T == NULL)
        return NULL;           /* 空树中未找到 */
    if (X < T->Element)        /* 若小于根节点 */
        return Find(X, T->Left);   /* 在左子树中搜索 */
    else if (X > T->Element)   /* 若大于根节点 */
        return Find(X, T->Right);  /* 在右子树中搜索 */
    else                       /* 若 X == 根节点 */
        return T;              /* 找到 */
}
```

> 这些都是**尾递归（tail recursion）**——很容易转化为迭代。
>
> $T(N) = S(N) = O(d)$，其中 $d$ 是 $X$ 的深度。

**迭代版本：**
```c
Position Iter_Find(ElementType X, SearchTree T) {
    /* Find 的迭代版本 */
    while (T) {
        if (X == T->Element)
            return T;          /* 找到 */
        if (X < T->Element)
            T = T->Left;       /* 沿左路径向下移动 */
        else
            T = T->Right;      /* 沿右路径向下移动 */
    }
    return NULL;               /* 未找到 */
}
```

---

#### 找最小值 (FindMin)

**递归版本：**
```c
Position FindMin(SearchTree T) {
    if (T == NULL)
        return NULL;               /* 空树中未找到 */
    else if (T->Left == NULL)
        return T;                  /* 找到最左节点 */
    else
        return FindMin(T->Left);   /* 继续向左移动 */
}
```
$T(N) = O(d)$

---

#### 找最大值 (FindMax)

**迭代版本：**
```c
Position FindMax(SearchTree T) {
    if (T != NULL)
        while (T->Right != NULL)
            T = T->Right;          /* 继续向右移动找到最右节点 */
    return T;                      /* 返回 NULL 或最右节点 */
}
```
$T(N) = O(d)$

---

#### 插入 (Insert)

**思路概要：**

初始 BST：
```
      30
     /  \
    5    40
   /
  2
```

- **插入 80：**
  1. 检查 80 是否已在树中。
  2. 80 > 30，向右；80 > 40，向右 -> NULL。
  3. 将 80 插入为 40 的右孩子。

- **插入 35：**
  1. 检查 35 是否已在树中。
  2. 35 > 30，向右；35 < 40，向左 -> NULL。
  3. 将 35 插入为 40 的左孩子。

- **插入 25：**
  1. 检查 25 是否已在树中。
  2. 25 < 30，向左；25 > 5，向右 -> NULL。
  3. 将 25 插入为 5 的右孩子。

> 搜索关键字时遇到的最后一个节点将是新节点的**父节点**。

**实现：**
```c
SearchTree Insert(ElementType X, SearchTree T) {
    if (T == NULL) {
        /* 创建并返回一个单节点树 */
        T = malloc(sizeof(struct TreeNode));
        if (T == NULL)
            FatalError("Out of space!!!");
        else {
            T->Element = X;
            T->Left = T->Right = NULL;
        }
    }
    /* 若树存在 */
    else if (X < T->Element)
        T->Left = Insert(X, T->Left);
    else if (X > T->Element)
        T->Right = Insert(X, T->Right);
    /* 否则 X 已在树中，不执行任何操作 */

    return T;   /* 不要忘记这行！！ */
}
```
$T(N) = O(d)$

> **问题：** 如何处理重复的关键字 (duplicate keys)？
> - **答案：** 一种方法是在每个节点中添加一个计数字段，或者一致地将它们存储在左子树或右子树中。

---

#### 删除 (Delete)

根据待删除节点的度分为三种情况 (three cases based on degree)：

##### 情况 1：删除叶子节点（度为 0）(Case 1: Delete a Leaf Node)
**将其父节点链接重置为 NULL**，然后释放该节点。

##### 情况 2：删除度为 1 的节点 (Case 2: Delete a Node with One Child)
**用该节点的唯一孩子替换该节点。**

```
删除前：          删除后：
   P               P
    \               \
     X               C
    /
   C
```

##### 情况 3：删除度为 2 的节点 (Case 3: Delete a Node with Two Children)
1. **用其左子树中的最大元素**（左子树的最右节点）或**右子树中的最小元素**（右子树的最左节点）**替换该节点的元素**。
2. **从子树中删除替换节点**（这将属于情况 1 或情况 2）。

**示例：从 BST 中删除 60**
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

- **方案 1（使用左子树）：** 用 55（左子树中的最大值）替换 60，然后从左子树中删除 55。
- **方案 2（使用右子树）：** 用右子树中的最小元素（即 70）替换 60，然后从右子树中删除原来的 70。

更详细的示例树：
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

删除 60：
- 在 60 的右子树中找最小值：FindMin(60 的右子树 = 70) -> 70（最左节点）。
- 用 70 替换 60 的元素。
- 从右子树中删除原来的 70。

或者：
- 在 60 的左子树中找最大值：FindMax(60 的左子树 = 50 的右子树) -> 55。
- 用 55 替换 60 的元素。
- 从左子树中删除 55（55 是度为 1 的节点，因此用 52 替换它）。

**实现：**
```c
SearchTree Delete(ElementType X, SearchTree T) {
    Position TmpCell;

    if (T == NULL)
        Error("Element not found");
    else if (X < T->Element)        /* 向左 */
        T->Left = Delete(X, T->Left);
    else if (X > T->Element)        /* 向右 */
        T->Right = Delete(X, T->Right);
    else {                          /* 找到待删除元素 */
        if (T->Left && T->Right) {  /* 两个孩子 */
            /* 用右子树中的最小元素替换 */
            TmpCell = FindMin(T->Right);
            T->Element = TmpCell->Element;
            T->Right = Delete(T->Element, T->Right);
        }
        else {                      /* 一个或零个孩子 */
            TmpCell = T;
            if (T->Left == NULL)    /* 同时也处理了零个孩子的情况 */
                T = T->Right;
            else if (T->Right == NULL)
                T = T->Left;
            free(TmpCell);
        }
    }
    return T;
}
```
$T(N) = O(h)$，其中 $h$ 是树的高度。

---

### 懒惰删除 (Lazy Deletion)

**思想：** 如果删除操作不多，可以在每个节点中添加一个**标志位（flag）**，用于标记节点是**有效的（active）** 还是**已删除的（deleted）**。

- 我们可以"删除"一个节点而不实际释放该节点的空间。
- 如果删除的关键字被重新插入，则无需再次调用 `malloc`。

**权衡：** 如果删除节点的数量与有效节点的数量相当，树的高度可能会增加，从而可能影响操作的效率（因为树中会充满"死"节点）。

---

### 4. 平均情况分析 (Average-Case Analysis)

**问题：** 将 $n$ 个元素放入一个二叉搜索树中，这棵树能有多高？

**答案：** 高度取决于**插入的顺序 (insertion order)**。

#### 示例：插入 1, 2, 3, 4, 5, 6, 7

**顺序 1：** `4, 2, 1, 3, 6, 5, 7`
```
        4
      /   \
     2     6
    / \   / \
   1   3 5   7
```
高度 $h = 2$（最佳情况——平衡树, balanced tree）

**顺序 2：** `1, 2, 3, 4, 5, 6, 7`
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
高度 $h = 6$（最坏情况——斜树）

#### 关键结论 (Key Conclusions)：
- 如果插入是随机的，BST 节点的**平均深度 (average depth)** 为 $O(\log N)$。
- **最坏情况深度 (worst-case depth)** 为 $O(N)$（当元素按有序或近似有序的顺序插入时）。
- 随机构建的 BST 的期望高度 (expected height) 为 $O(\log N)$。

---

## 关键公式总结 (Key Formula Summary)

| 性质 (Property) | 公式 (Formula) |
|----------|---------|
| 第 $i$ 层最多节点数 | $2^{i-1}$ |
| 深度为 $k$ 的树中最多节点数 | $2^k - 1$ |
| 叶子节点与度为 2 的节点关系 | $n_0 = n_2 + 1$ |
| $N$ 个节点的树中的边数 | $N - 1$ |
| $n$ 个节点的二叉树中的空链接数 | $n + 1$ |
| BST 查找/插入时间（平均） | $O(\log N)$ |
| BST 查找/插入时间（最坏情况） | $O(N)$ |

## 遍历顺序总结 (Summary of Traversal Orders)

| 遍历方式 (Traversal) | 顺序 (Order) | 访问模式 (Visit Pattern) |
|-----------|-------|---------------|
| 先序遍历 | 根 -> 左 -> 右 | 在孩子之前访问 |
| 中序遍历 | 左 -> 根 -> 右 | 在孩子之间访问 |
| 后序遍历 | 左 -> 右 -> 根 | 在孩子之后访问 |
| 层序遍历 | 从上到下，从左到右 | 使用队列的 BFS |
