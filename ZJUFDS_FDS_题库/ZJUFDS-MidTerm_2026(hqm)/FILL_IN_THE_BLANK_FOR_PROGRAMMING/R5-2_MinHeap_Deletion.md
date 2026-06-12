## R5-2 MinHeap Deletion

- **类型**: FILL_IN_THE_BLANK_FOR_PROGRAMMING
- **分值**: 9 分
- **作者**: 陈越

---

Please fill in the blanks in the program which deletes a given element at position `p` from a min-heap `H`.

```c++
Deletion ( PriorityQueue H,  int p )  /* delete the element H->Elements[p] */
{
   ElementType temp;
   int child;

   temp = H-> Elements[ H->Size-- ];
   if ( temp Elements[p] ) {
      while ( (p != 1) && (temp Elements[p/2]) ) { 
         @`[    ]`;
         p /= 2;
      } 
   }
   else {
      while( (child = 2*p) Size) {
         if ( child != H->Size && @`[    ]` )
            child ++;
         if ( @`[    ]` ) {
            H->Elements[p] = H->Elements[child];
            p = child;
         }
         else
            break;
      }
   }
   H->Elements[p] = temp;
}
```
