## Normal A+B with Binary Search Trees

- **类型**: SUBJECTIVE
- **分值**: 100 分
- **作者**: 郑友怡

---

A Binary Search Tree (BST) is recursively defined as a binary tree which has the following properties:
- The left subtree of a node contains only nodes with keys less than the node's key.
- The right subtree of a node contains only nodes with keys greater than or equal to the node's key.
- Both the left and right subtrees must also be binary search trees.

Given two binary search trees T1 and T2, and an integer N, you are supposed to find a number A from T1 and B from T2 such that A+B=N.

### Input Specification:
Each input file contains one test case.  Each case gives the information of T1, T2 and N, in the following format:
The first line contains a positive integer $n_1$ ($ \le 2\times 10^5$), which is the number of nodes in T1.  Then $n_1$ lines follow, where the $i$th line contains the key value $k$ ($-2\times 10^9 \le k \le 2\times 10^9$) and the parent node index of the $i$th node ($0\le i < n_1$).  Since the root has no parent, its parent index is defined to be $-1$.
After T1, T2 is given in the same format of T1.
Finally the last line gives the target N (with the same range of $k$).

### Output Specification:
For each test case, print in a line `true` if at least one solution does exist, then output all the solutions in the following lines, each in the format `N = A + B`.  In case the solution is not unique, you must output them in ascending order of the values of `A`.  Note: the same equation should be printed only once.  If there is no solution, simply print `false`.
Then print in the last two lines the preorder traversal sequences of T1 and T2, respectively.  The values in each line are separated by 1 space, and there must be no extra space at the beginning or the end of the line.
### Sample Input 1:
```in
8
12 2
16 5
13 4
18 5
15 -1
17 4
14 2
18 3
7
20 -1
16 0
25 0
13 1
18 1
21 2
28 2
36
```
### Sample Output 1:
```out
true
36 = 15 + 21
36 = 16 + 20
36 = 18 + 18
15 13 12 14 17 16 18 18
20 16 13 18 25 21 28
```
### Sample Input 2:
```in
5
10 -1
5 0
15 0
2 1
7 1
3
15 -1
10 0
20 0
40
```
### Sample Output 2:
```out
false
10 5 2 7 15
15 10 20
```
### Grading Policy:

- Chapter 1: Introduction (6 pts.)
- Chapter 2: Algorithm Specification (12 pts.)
- Chapter 3: Testing Results (20 pts.)
- Chapter 4: Analysis and Comments (10 pts.)
- Write the program (50 pts.) with sufficient comments.
- Overall style of documentation (2 pts.)
