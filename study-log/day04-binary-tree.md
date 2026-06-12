# Day 4 — 二叉树 (Binary Trees)

---

## Step 1: 树的术语

### 定义与例子

```
        A              ← 根节点 (Root)
      / | \
     B  C  D           ← B/C/D 是 A 的孩子 (Children)
    / \    /|\
   E   F  H I J        ← 叶子节点 (Leaf)
  / \
 K   L
```

### 纠错记录

| 术语 | 第一次说错的 | 正确的 |
|------|-----------|--------|
| 度 (Degree) | 叶子数 | 直接孩子的数量。A 的度=3, B 的度=2, C 的度=0 |
| 树的度 | — | 所有节点度的最大值。上图树的度=3 |
| 叶子 (Leaf) | "就是孩子" ❌ | 度=0 的节点。C/F/K/L/H/I/J |
| 路径长度 | 节点数 | 边的数量。A→F 长度为2 |
| 深度 vs 高度 | 一个意思 | 深度从上往下，高度从下往上 |

### 深度 vs 高度（经典考点）

```
        A
      /   \
     B     C
    / \
   D   E
```

| 节点 | 深度 (Depth) | 高度 (Height) |
|------|:-----------:|:------------:|
| A | 0 | 2 |
| B | 1 | 1 |
| C | 1 | 0 |
| D | 2 | 0 |

- 深度 = 从根到该节点的边数（根深度=0）
- 高度 = 从该节点到最深叶子的边数（叶子高度=0）
- 树的高度 = 根的高度

> ✅ 已纠正

---

## Step 2: 二叉树三个核心性质

1. 第 i 层最多 $2^{i-1}$ 个节点
2. 深度 k 的树最多 $2^k - 1$ 个节点
3. $n_0 = n_2 + 1$（叶子数 = 度为2的节点数 + 1）

> ✅ 已掌握

---

## Step 3: 四大遍历（递归版）

```
        A
      /   \
     B     C
    / \   / \
   D   E F   G
```

| 遍历 | 顺序 | 结果 |
|------|------|------|
| 先序 (Preorder) | 根→左→右 | A B D E C F G |
| 中序 (Inorder) | 左→根→右 | D B E A F C G |
| 后序 (Postorder) | 左→右→根 | D E B F G C A |
| 层序 (Levelorder) | 逐层 | A B C D E F G |

### 递归遍历代码（三者区别只在 printf 位置）

```c
void preorder(TreeNode* root) {
    if (root == NULL) return;
    printf("%d ", root->data);   // 先
    preorder(root->left);
    preorder(root->right);
}

void inorder(TreeNode* root) {
    if (root == NULL) return;
    inorder(root->left);
    printf("%d ", root->data);   // 中
    inorder(root->right);
}

void postorder(TreeNode* root) {
    if (root == NULL) return;
    postorder(root->left);
    postorder(root->right);
    printf("%d ", root->data);   // 后
}
```
> ✅ 已掌握

---

## Step 4: 层序遍历（队列 + 循环队列）

### 完整代码

```c
#define MAX 100

typedef struct TreeNode {
    int data;
    struct TreeNode *left, *right;
} TreeNode;

typedef struct {
    TreeNode* data[MAX];
    int front, rear;
} Queue;

void init(Queue* q) { q->front = 0; q->rear = 0; }

void enqueue(Queue* q, TreeNode* root) {
    if ((q->rear + 1) % MAX == q->front) return;
    q->rear = (q->rear + 1) % MAX;
    q->data[q->rear] = root;
}

TreeNode* dequeue(Queue* q) {
    if (q->rear == q->front) return NULL;
    // BUG记录1: 忘记判空 return NULL
    TreeNode* t = q->data[q->front];
    q->front = (q->front + 1) % MAX;
    return t;
}

int isEmpty(Queue* q) { return q->rear == q->front; }

void levelorder(TreeNode* root) {
    if (root == NULL) return;
    Queue q; init(&q);
    enqueue(&q, root);
    while (!isEmpty(&q)) {
        TreeNode* node = dequeue(&q);
        printf("%d ", node->data);
        // BUG记录2: 两次写成 &p 而不是 &q
        if (node->left != NULL)
            enqueue(&q, node->left);
        if (node->right != NULL)
            enqueue(&q, node->right);
    }
}
```
> ✅ 已掌握

---
