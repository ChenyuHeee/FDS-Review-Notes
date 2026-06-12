## 2-9 Given a doubly linked list $L$ with $n$ ($n ≥ 3$) nodes and a du

- **类型**: MULTIPLE_CHOICE
- **分值**: 2 分
- **作者**: 考研真题

---

Given a doubly linked list $L$ with $n$ ($n\ge 3$) nodes and a dummy header.  Each node has the structure  ![捕获.JPG](~/795963fe-d8a0-41f9-aab6-c80f48746f78.JPG) where $p_2$ and $p_1$ are pointers to its immediate predecessor and immediate successor, respectively.  The pointer `head` points to the head node, and the pointer `cu = head`.  Now you are supposed to change $p_2$ of every node in $L$ to the immediate successor of $p_1$.  If the node does not exist, the pointer must be NULL.  Then among the following statements, which one can get the job done correctly?

A. ```
while(cu!=NULL){cu->p2=cu->p1->p1; cu=cu->p1;}
```
B. ```
while(cu!=NULL&&cu->p2!=NULL){cu->p2=cu->p1->p1; cu=cu->p1;}
```
C. ```
while(cu!=NULL){if(cu->p1!=NULL){cu->p2=cu->p1->p1; cu=cu->p1;}}
```
D. ```
while(cu!=NULL){if(cu->p1!=NULL) cu->p2=cu->p1->p1; else cu->p2=NULL; cu=cu->p1;}
```
