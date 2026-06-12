## 7-2 Queue Using Two Stacks

- **类型**: PROGRAMMING
- **分值**: 8 分
- **作者**: 陈越

---

A queue (FIFO structure) can be implemented by two stacks (LIFO structure) in the following way:

1. Start from two empty stacks $s_1$ and $s_2$.
2. When element $e$ is enqueued, it is actually pushed onto $s_1$.
3. When we are supposed to dequeue, $s_2$ is checked first.  If $s_2$ is empty, everything in $s_1$ will be transferred to $s_2$ by popping from $s_1$ and immediately pushing onto $s_2$.  Then we just pop from $s_2$ -- the top element of $s_2$ must be the first one to enter $s_1$ thus is the first element that was enqueued.

Assume that each operation of push or pop takes 1 unit of time.  You job is to tell the time taken for each dequeue.

### Input Specification:

Each input file contains one test case. For each case, the first line gives a positive integer $N$ ($\le 10^3$), which are the number of operations. Then $N$ lines follow, each gives an operation in the format

```
Operation Element
```

where `Operation` being `I` represents enqueue and `O` represents dequeue.  For each `I`, `Element` is a positive integer that is no more than $10^6$.  No `Element` is given for `O` operations.\
It is guaranteed that there is at least one `O` operation.

### Output Specification:

For each dequeue operation, print in a line the dequeued element and the unites of time taken to do this dequeue.  The numbers in a line must be separated by 1 space, and there must be no extra space at the beginning or the end of the line.\
In case that the queue is empty when dequeue is called, output in a line `ERROR` instead.

### Sample Input:

```in
10
I 20
I 32
O
I 11
O
O
O
I 100
I 66
O
```

### Sample Output:

```out
20 5
32 1
11 3
ERROR
100 5
```

- **时间限制**: 400 ms
- **内存限制**: 65536 KB
