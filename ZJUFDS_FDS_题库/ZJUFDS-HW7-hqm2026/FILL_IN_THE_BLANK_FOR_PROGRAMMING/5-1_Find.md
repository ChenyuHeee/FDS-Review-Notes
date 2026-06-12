## 5-1 Find

- **类型**: FILL_IN_THE_BLANK_FOR_PROGRAMMING
- **分值**: 6 分
- **作者**: 陈越

---

Please fill in the blanks in the program which performs `Find` as a Union/Find operation with path compression.

```c++
SetType Find ( ElementType X, DisjSet S )
{   
   ElementType root, trail, lead;

   for ( root = X; S[root] > 0; @`[    ]` ) ;  
   for ( trail = X; trail != root; trail = lead ) {
      lead = S[trail] ;   
      @`[    ]`;   
   } 
   return root;
}
```
