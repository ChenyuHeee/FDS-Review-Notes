## 5-1 Insertion Sort

- **类型**: FILL_IN_THE_BLANK_FOR_PROGRAMMING
- **分值**: 2 分
- **作者**: 陈越

---

The function is to sort `N` elements in non-decreasing order by Insertion Sort.
以下函数的功能是将 `N` 个元素用插入排序算法按**非递减**顺序排序。

```c++
void InsertionSort( ElementType A[ ], int N ) 
{
    int P, i;
    ElementType Tmp;
     
    for ( P=1; P<N; P++ ) {
        Tmp = A[P];
        for ( i=P;  ; i-- )
            A[i] = A[i-1];
       ;
    }
}
```
