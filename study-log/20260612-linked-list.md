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

### Q&A: 为什么 `createNode` 返回的是 `Node*` 而不是 `Node`？

`malloc` 在堆上分配内存，返回的是**地址（指针）**，不在栈上。

| 写法 | 后果 |
|------|------|
| `Node createNode()` | 返回结构体的**副本**，修改副本不影响链表 |
| `Node* createNode()` | 返回节点的**地址**，直接操作链表中的原节点 |

链表操作的是节点的地址（指针），不是节点本身，所以所有相关函数都返回 `Node*`。

---

### 下一步预告

用 `createNode` 创建 3 个节点，并把它们串起来（10 → 20 → 30 → NULL）。

---

## Step 3: 建链（创建并链接节点）

**题目**：用 `createNode` 创建 3 个节点，串成 10 → 20 → 30 → NULL。

### Q&A: 怎么调用 createNode 并链接？

用指针接住 `createNode` 的返回值，然后改 `->next`：

```c
Node* head = createNode(10);
head->next = createNode(20);
head->next->next = createNode(30);
// 30->next 已经是 NULL
```

> ✅ 已掌握

---

---

## Step 4: 遍历链表 (Traversal)

**题目**：写 `printList` 打印所有节点。

```c
void printList(Node* head) {
    Node* p = head;
    while (p != NULL) {
        printf("%d ", p->data);
        p = p->next;
    }
}
```
> ✅ 已掌握

---

## Step 5: 插入节点

**题目**：在指定节点后面插入新节点。

**易错点记录**：
- `Node* p;` 只有声明，没有 `malloc` → 野指针崩溃
- `p->next = node->next` 和 `node->next = p` 顺序不能反

```c
void insertAfter(Node* node, int x) {
    Node* p = createNode(x);
    p->next = node->next;    // 先
    node->next = p;          // 后
}
```
> ✅ 已掌握

---

## Step 6: 头部插入

**Q&A: 为什么 `Node**`？** 修改 `main` 中的 head 本身，必须传指针的地址。

```c
void insertHead(Node** head, int x) {
    Node* p = createNode(x);
    p->next = *head;
    *head = p;
}
```

---

## Step 7: 删除指定值的节点

**易错点记录（多轮修正）**：
1. `*head->data` 优先级错误 → `(*head)->data`
2. 删了节点忘记 `free` → 内存泄漏
3. `p->next == NULL` 时访问 `p->next->data` → 崩溃 → 先判空
4. `x` 找不到时 `p->next->next` 崩溃 → 加 `if (p->next != NULL)` 保护

最终正确版本：

```c
void deleteNode(Node** head, int x) {
    // BUG记录1: 最早写成 *head->data，但 -> 优先级比 * 高
    //          实际等价于 *(head->data)，错误。必须写成 (*head)->data
    if ((*head)->data == x) {
        // BUG记录2: 第一次写 head=&(*head->next)，只改了局部形参
        //          正确写法是 *head = ...
        // BUG记录3: 忘了 free(tmp)，内存泄漏
        Node* tmp = *head;
        *head = (*head)->next;
        free(tmp);
        return;
    }
    Node* p = *head;
    // BUG记录4: 第一次写 while(p->next->data!=x)，如果 p->next 是 NULL
    //          访问 p->next->data 直接崩溃。必须把 NULL 检查放在前面
    while (p->next != NULL && p->next->data != x) {
        // BUG记录5: 第一次把 NULL 检查放在 while 体里 (p == NULL)
        //          但崩在条件判断里，根本走不到体里，检查晚了
        p = p->next;
    }
    // BUG记录6: 没找到 x 时 p->next 是 NULL，直接 p->next->next 会崩
    if (p->next == NULL) return;
    Node* tmp = p->next;
    // BUG记录7: 忘了 free(tmp)，内存泄漏
    p->next = p->next->next;
    free(tmp);
}
```
> ✅ 最终通过

---

## 下一步预告

栈（数组实现）。
