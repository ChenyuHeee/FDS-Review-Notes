# 2026-06-28 — 排序 速通：快排 / 表排 / 桶排 / 基数排

---

## 快速排序 (Quicksort)

**思想**：选 pivot，小的放左，大的放右，递归。

### 分区过程（最关键）

```c
// 取 A[right] 为 pivot
// 返回 pivot 最终位置
int partition(int A[], int left, int right) {
    int pivot = A[right];
    int i = left;                       // i: 小元素区域的边界
    
    for (int j = left; j < right; j++) {
        if (A[j] < pivot) {             // 遇到比 pivot 小的
            swap(&A[i], &A[j]);         // 换到小区域
            i++;
        }
    }
    swap(&A[i], &A[right]);             // pivot 放到中间
    return i;                           // 返回 pivot 位置
}
```

### 手跑例子

```
A = [5, 2, 6, 1, 4, 9, 3, 7], pivot = A[7] = 7

j=0: 5<7 → swap(A[0],A[0]) → i=1  [5,2,6,1,4,9,3,7]
j=1: 2<7 → swap(A[1],A[1]) → i=2  [5,2,6,1,4,9,3,7]
j=2: 6<7 → swap(A[2],A[2]) → i=3  [5,2,6,1,4,9,3,7]
j=3: 1<7 → swap(A[3],A[3]) → i=4  [5,2,6,1,4,9,3,7]
j=4: 4<7 → swap(A[4],A[4]) → i=5  [5,2,6,1,4,9,3,7]
j=5: 9<7? NO → 不动              [5,2,6,1,4,9,3,7]
j=6: 3<7 → swap(A[5],A[6]) → i=6  [5,2,6,1,4,3,9,7]
最后 swap(A[6],A[7]) → [5,2,6,1,4,3,7,9]
pivot=7 在位置6，左边全 < 7，右边全 > 7
```

### 主函数

```c
void quickSort(int A[], int left, int right) {
    if (left >= right) return;
    int p = partition(A, left, right);   // 分区
    quickSort(A, left, p - 1);            // 排左边
    quickSort(A, p + 1, right);           // 排右边
}
```

### 优化（median-of-three 选 pivot）

```c
// 取 left/mid/right 三个数的中值作为 pivot，放到 right-1
ElementType Median3(ElementType A[], int left, int right) {
    int center = (left + right) / 2;
    if (A[left] > A[center])  swap(&A[left], &A[center]);
    if (A[left] > A[right])   swap(&A[left], &A[right]);
    if (A[center] > A[right]) swap(&A[center], &A[right]);
    swap(&A[center], &A[right - 1]);  // pivot 藏在 right-1
    return A[right - 1];
}
```

### 复杂度

| 情况 | 复杂度 | 条件 |
|------|--------|------|
| 最坏 | $O(N^2)$ | pivot 每次选到最小/最大 |
| 平均 | $O(N \log N)$ | |
| 最好 | $O(N \log N)$ | |

---

## 表排序 (Table Sort)

**动机**：排序的是大结构体（如学生记录），移动代价很高。用一种廉价的方式——**只移动指针/索引**。

```
原始数据: A = [f, d, a, b, e, c]    ← 不移动
Table:    T = [0, 1, 2, 3, 4, 5]
排序后:   T = [2, 3, 5, 1, 4, 0]   ← 只改了 T
         T[0]=2 → A[2]=a (最小)
         T[1]=3 → A[3]=b
         ...
```

**物理重排**（如果必须移动 A）：利用置换环 (cycle)：

```c
// T 是已排序的索引表
// 原地重排 A
void rearrange(ElementType A[], int T[], int N) {
    for (int i = 0; i < N; i++) {
        if (T[i] != i) {               // 不在正确位置
            ElementType tmp = A[i];
            int j = i;
            while (T[j] != i) {        // 遍历一个环
                A[j] = A[T[j]];
                int next = T[j];
                T[j] = j;              // 标记已处理
                j = next;
            }
            A[j] = tmp;
            T[j] = j;
        }
    }
}
```

最坏 $\lfloor 3N/2 \rfloor$ 次移动。

---

## 桶排序 (Bucket Sort)

**思想**：不是比较，而是"分类投放"。

**例子**：给 1000 个学生的成绩 (0~100) 排序。

```c
void bucketSort(int A[], int N) {
    int count[101] = {0};
    for (int i = 0; i < N; i++)
        count[A[i]]++;                // 统计每个分数的人数
    for (int i = 0; i <= 100; i++)
        while (count[i]-- > 0)
            printf("%d ", i);          // 按顺序输出
}
```

$T(N) = O(M + N)$，M 是桶的数量。**不基于比较**，所以能突破 $O(N \log N)$ 下界。

---

## 基数排序 (Radix Sort) — LSD

**思想**：按每一位排序，从最低位 (LSD) 到最高位 (MSD)。

```
初始: 64, 8, 216, 512, 27, 729, 0, 1, 343, 125

Pass 1 (个位):
0→0 | 1→1 | 2→512 | 3→343 | 4→64 | 5→125 | 6→216 | 7→27 | 8→8 | 9→729
收集: 0, 1, 512, 343, 64, 125, 216, 27, 8, 729

Pass 2 (十位):
0→0,1,8 | 1→512,216 | 2→125,27,729 | 3→343 | 6→64 | 7→ | ...
收集: 0, 1, 8, 512, 216, 125, 27, 729, 343, 64

Pass 3 (百位):
0→0,1,8,27,64 | 1→125 | 2→216 | 3→343 | 5→512 | 7→729
收集: 0, 1, 8, 27, 64, 125, 216, 343, 512, 729 ✅
```

$T(N) = O(P \cdot (N + B))$，P = 位数，B = 基数。不基于比较。

---

## 排序全总结

| 排序 | 平均 | 最坏 | 空间 | 稳定 | 基于比较 |
|------|------|------|------|:--:|:--:|
| 插入 | $O(N^2)$ | $O(N^2)$ | $O(1)$ | 稳定 | 是 |
| 希尔 | $O(N^{3/2})$ | $O(N^2)$ | $O(1)$ | 不稳定 | 是 |
| 堆排 | $O(N \log N)$ | $O(N \log N)$ | $O(1)$ | 不稳定 | 是 |
| 归并 | $O(N \log N)$ | $O(N \log N)$ | $O(N)$ | 稳定 | 是 |
| 快排 | $O(N \log N)$ | $O(N^2)$ | $O(\log N)$ | 不稳定 | 是 |
| 桶排 | $O(M+N)$ | — | $O(M)$ | 稳定 | 否 |
| 基数 | $O(P(N+B))$ | — | $O(N+B)$ | 稳定 | 否 |

**比较排序下界**：$\Omega(N \log N)$。桶排和基数排不比较，所以能突破。
