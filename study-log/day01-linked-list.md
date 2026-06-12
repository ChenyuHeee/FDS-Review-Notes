# Day 1 — 链表 (Linked List)

---

## Step 1: 节点结构

**复习概念**：链表每个节点包含 data 和 next 指针。

```c
typedef struct node {
    int data;
    struct node *next;
} Node;
```

> ✅ 已掌握

---

## Step 2: 创建节点

**题目**：写一个 `createNode(int x)` 函数，创建一个值为 x 的新节点，返回其指针。

### 你的实现

```c
// 待填写
```

### 标准答案

```c
Node* createNode(int x) {
    Node* p = (Node*)malloc(sizeof(Node));
    p->data = x;
    p->next = NULL;
    return p;
}
```

### 要点

- `malloc` 返回的指针必须检查是否为 NULL
- 新节点的 `next` 必须初始化为 NULL，避免野指针
- `sizeof(Node)` 不要写成 `sizeof(Node*)` —— 差别很大

---

### 下一步预告

用 `createNode` 创建 3 个节点，并把它们串起来（10 → 20 → 30 → NULL）。
