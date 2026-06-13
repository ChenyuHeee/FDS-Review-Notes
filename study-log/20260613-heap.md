# Day 6 — 堆 (Heap)

---

## Step 1: 完全二叉树

### Q&A: 什么是完全二叉树？

除了最后一层，其他层全满。最后一层节点从左到右连续排列。

```
✅ 完全：           ❌ 不是完全：
     A                  A
   /   \              /   \
  B     C            B     C
 / \   /            / \     \
D   E F            D   E     F
                           ↑ 左孩子空缺
```

### Q&A: n/2+1 是不是叶子？

n=8 时：n/2=4 是最后一个非叶（只有孩子 8），n/2+1=5 没有孩子（2*5=10>8），是叶子。结论成立。

### Q&A: 数组索引公式只用于堆

| 公式 | 用途 | 适用范围 |
|------|------|---------|
| parent = i/2 | 上滤 | 只用于数组实现的完全二叉堆 |
| left = 2*i | 下滤 | 同上 |
| right = 2*i+1 | 下滤 | 同上 |

不适用于 BST、链表、图等其他数据结构。

> ✅

---

## Step 2: Insert（上滤 Percolate Up）

### 手画过程

初始堆: `[1]=3, [2]=6, [3]=5, [4]=9, [5]=10, size=5`

Insert(2):

```
存放 data[6] → 跟 data[3]=5 换 → 跟 data[1]=3 换 → 到根停
结果: [1]=2, [2]=6, [3]=3, [4]=9, [5]=10, [6]=5
```

### 代码

```c
void insert(Heap* h, int x) {
    if (h->size >= MAX - 1) return;
    // BUG记录1: 第一次写成 h->size++，后自增导致 x 放在旧位置
    int i = ++(h->size);
    // BUG记录2: 循环里写了 h->data[i/2] = x，不应该交换，只应该父下沉
    // BUG记录3: 漏了循环外的 h->data[i] = x
    while (i > 1 && h->data[i/2] > x) {
        h->data[i] = h->data[i/2];   // 父下沉
        i = i / 2;                   // 空穴上移
    }
    h->data[i] = x;
}
```

> ✅

---

## Step 3: DeleteMin（下滤 Percolate Down）

### 代码

```c
int deleteMin(Heap* h) {
    int min = h->data[1];
    int x = h->data[h->size--];
    int i = 1, child;
    // BUG记录1: while 条件里 size 忘了写 h-> → 编译错误
    // BUG记录2: if 条件里 size 同样问题
    // BUG记录3: 漏了最后 h->data[i] = x
    while (2*i <= h->size) {
        child = i * 2;
        if (child + 1 <= h->size && h->data[child + 1] < h->data[child])
            child++;
        if (x <= h->data[child]) break;
        h->data[i] = h->data[child];
        i = child;
    }
    h->data[i] = x;
    return min;
}
```

> ✅

---

## Step 4: BuildHeap（Floyd 法，$O(N)$）

### 核心思路

- 从 n/2（最后一个非叶）开始，到 1 为止
- 每个节点做一次 Percolate Down
- 叶子不用处理（本身就是堆）

### Q&A: Floyd 法 vs 逐个 Insert

| 方法 | 复杂度 |
|------|--------|
| n 次 Insert | $O(N\log N)$ |
| Floyd (n/2 次 PercDown) | $O(N)$ |

$O(N)$ 原因：底层叶子几乎不下沉，总和接近 N 而非 N log N。

### 代码

```c
void percDown(Heap* h, int i) {
    int x = h->data[i];
    int child;
    while (2*i <= h->size) {
        child = i * 2;
        if (child + 1 <= h->size && h->data[child + 1] < h->data[child])
            child++;
        if (x <= h->data[child]) break;
        h->data[i] = h->data[child];
        i = child;
    }
    h->data[i] = x;
}

void buildHeap(Heap* h, int arr[], int n) {
    // BUG记录1: 第一次写 arr+i（指针地址），应该取值 arr[i]
    // BUG记录2: 用 for 循环 ++size，应该直接 h->size = n
    // BUG记录3: Floyd 循环写成 while(j!=1) + j/=2 + percDown(j) and percDown(j+1)
    //          应该是简单的 for (j=n/2; j>=1; j--)
    for (int i = 0; i < n; i++)
        h->data[i+1] = arr[i];
    h->size = n;
    for (int j = n / 2; j >= 1; j--)
        percDown(h, j);
}
```

> ✅

---

## 堆操作总结

| 操作 | 关键步骤 | 复杂度 |
|------|---------|--------|
| Insert | 上滤 (Percolate Up) | $O(\log N)$ |
| DeleteMin | 下滤 (Percolate Down) | $O(\log N)$ |
| BuildHeap | n/2 → 1 逐一下滤 | $O(N)$ |
