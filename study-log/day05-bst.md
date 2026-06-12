# Day 5 — 二叉搜索树 (BST)

---

## Step 1: Find

```c
TreeNode* find(TreeNode* root, int x) {
    if (root == NULL) return NULL;
    if (x < root->data) return find(root->left, x);
    if (x > root->data) return find(root->right, x);
    return root;
}
```
> ✅

---

## Step 2: FindMin / FindMax

```c
// BUG记录: 第一次返回类型 int，空树无法处理 → 改为返回 TreeNode*
TreeNode* findMin(TreeNode* root) {
    if (root == NULL) return NULL;
    if (root->left == NULL) return root;
    return findMin(root->left);
}

TreeNode* findMax(TreeNode* root) {
    if (root == NULL) return NULL;
    if (root->right == NULL) return root;
    return findMax(root->right);
}
```
> ✅

---

## Step 3: Insert

```c
TreeNode* insert(TreeNode* root, int x) {
    if (root == NULL) {
        // BUG记录1: 第一次写 root->data = x，root 是 NULL 直接崩溃
        // BUG记录2: 忘记初始化 left = right = NULL
        TreeNode* node = (TreeNode*)malloc(sizeof(TreeNode));
        node->data = x;
        node->left = node->right = NULL;
        return node;
    }
    if (x < root->data)
        root->left = insert(root->left, x);    // BUG记录3: 忘记 root->left = 来接住返回值
    else if (x > root->data)
        root->right = insert(root->right, x);
    // x == root->data：啥也不做
    return root;
}
```
> ✅

---

## Step 4: Delete（最重要的 BST 操作）

Delete 分三种情况：
- **Case 1**：叶子 → 直接 free
- **Case 2**：一个孩子 → 用孩子替代
- **Case 3**：两个孩子 → 找替身（左子树最大或右子树最小），替换值后删替身

```c
TreeNode* delete(TreeNode* root, int x) {
    if (root == NULL) return NULL;

    if (x < root->data)
        root->left = delete(root->left, x);
    else if (x > root->data)
        root->right = delete(root->right, x);
    else {
        // === 找到了 root 就是要删的节点 ===

        // Case 1: 叶子
        if (root->left == NULL && root->right == NULL) {
            // BUG记录1: 第一次只写了 return NULL，没 free → 内存泄漏
            // BUG记录2: 早期版本在外层先判断是不是叶子，不是目标就不处理
            //          走到 return root 不会继续递归，导致跳过目标节点
            free(root);
            return NULL;
        }
        // Case 2: 只有一个孩子
        // BUG记录3: 第一次 left/right 写反了
        //          只有一个左孩子时应该返回左孩子，写成了返回右孩子
        else if (root->left == NULL) {   // 只有右孩子
            TreeNode* tmp = root->right;
            free(root);
            return tmp;
        }
        else if (root->right == NULL) {  // 只有左孩子
            TreeNode* tmp = root->left;
            free(root);
            return tmp;
        }
        // Case 3: 两个孩子 → 找左子树最大值替换
        else {
            // BUG记录4: 第一次写 FindMax(root) → 找的是整棵树的最大值
            //          应该是 FindMax(root->left)，找左子树的最大值
            // BUG记录5: 第一次写 delete(max, max->data) → max 不是树的根
            //          应该写 root->left = delete(root->left, max->data)
            TreeNode* max = findMax(root->left);
            root->data = max->data;
            root->left = delete(root->left, max->data);
        }
    }
    return root;
}
```
> ✅

---

## 本日知识点总结

| 操作 | 复杂度 | 核心要点 |
|------|--------|---------|
| Find | $O(h)$ | 跟二分查找一样，左小右大 |
| FindMin/Max | $O(h)$ | 一直左/一直右 |
| Insert | $O(h)$ | 等于 Find + 在 NULL 处创建新节点 |
| Delete | $O(h)$ | Case 3 转 Case 1/2 |
