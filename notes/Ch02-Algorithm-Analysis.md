# Chapter 2: Algorithm Analysis

---

## 1. Definition of an Algorithm

**【Definition】** An **algorithm** is a finite set of instructions that, if followed, accomplishes a particular task. All algorithms must satisfy the following five criteria:

1. **Input** -- There are zero or more quantities that are externally supplied.
2. **Output** -- At least one quantity is produced.
3. **Definiteness** -- Each instruction is clear and unambiguous.
4. **Finiteness** -- If we trace out the instructions of an algorithm, then for all cases, the algorithm terminates after a finite number of steps.
5. **Effectiveness** -- Every instruction must be basic enough to be carried out, in principle, by a person using only pencil and paper. It is not enough that each operation be definite (as in criterion 3); it also must be feasible.

### Algorithm vs. Program

- A **program** is written in some programming language and does **not** have to be finite (e.g., an operating system runs indefinitely).
- An **algorithm** can be described by human languages, flow charts, some programming languages, or pseudo-code.

### Example: Selection Sort

Sort a set of $n \ge 1$ integers in increasing order.

**Idea:** From those integers that are currently unsorted, find the smallest and place it next in the sorted list.

```
for ( i = 0; i < n; i++ ) {
    Examine list[i] to list[n-1] and suppose that the smallest integer is at list[min];
    Interchange list[i] and list[min];
}
```

**In words:** Sort = Find the smallest integer + Interchange it with list[i].

---

## 2. What to Analyze

### Two Types of Complexity

| Type | Description |
|------|-------------|
| **Machine & compiler-dependent run times** | Actual runtime depends on hardware, compiler, etc. |
| **Time & space complexities** | Machine & compiler-independent measures |

### Assumptions for Analysis

1. Instructions are executed **sequentially**.
2. Each instruction is **simple** and takes exactly **one time unit**.
3. Integer size is **fixed** and we have **infinite memory**.

### Functions Typically Analyzed

- $T_{avg}(N)$ -- the **average case** time complexity as a function of input size $N$.
- $T_{worst}(N)$ -- the **worst case** time complexity as a function of input size $N$.

If there is more than one input, these functions may have more than one argument.

---

### Example: Matrix Addition

```c
void add( int a[][MAX_SIZE],
          int b[][MAX_SIZE],
          int c[][MAX_SIZE],
          int rows, int cols )
{
    int i, j;
    for ( i = 0; i < rows; i++ )          /* rows + 1 */
        for ( j = 0; j < cols; j++ )      /* rows * (cols + 1) */
            c[i][j] = a[i][j] + b[i][j];  /* rows * cols */
}
```

**Step count:**
- Outer loop: `rows + 1` (including the last failed test)
- Inner loop initialization: `rows * (cols + 1)`
- Inner assignment: `rows * cols`

$$T(rows, cols) = 2 \cdot rows \cdot cols + 2 \cdot rows + 1$$

> **Note:** If $rows \gg cols$, exchange rows and cols to improve performance.

---

### Example: Iterative Sum

```c
float sum( float list[], int n )
{   /* add a list of numbers */
    float tempsum = 0;          /* count = 1 */
    int i;
    for ( i = 0; i < n; i++ )   /* count++ */
        tempsum += list[i];     /* count++ */
    return tempsum;             /* count++ for last execution of for */
}
```

$$T_{sum}(n) = 2n + 3$$

---

### Example: Recursive Sum

```c
float rsum( float list[], int n )
{   /* add a list of numbers */
    if ( n )                         /* count++ */
        return rsum(list, n-1) + list[n-1];  /* count++ */
    return 0;                        /* count++ */
}
```

$$T_{rsum}(n) = 2n + 2$$

> **Important:** Although $2n+2 < 2n+3$ numerically, the recursive function takes more time to **compute each step** due to function call overhead. If you try a large $n$, you will find the recursive version is actually slower!

### Key Insight

Counting the exact number of steps is **too complicated** and not always worth the effort. What we really care about is the **growth rate** of the running time as input size changes -- i.e., the **asymptotic behavior**.

---

## 3. Asymptotic Notation ($O$, $\Omega$, $\Theta$, $o$)

The point of counting steps is to **predict the growth in run time as $N$ changes**, and thereby compare the time complexities of two programs. What we really want to know is the **asymptotic behavior** of $T_p$.

**Key insight:** Suppose $T_{p1}(N) = c_1 N^2 + c_2 N$ and $T_{p2}(N) = c_3 N$. No matter what $c_1, c_2, c_3$ are, there will be an $n_0$ such that $T_{p1}(N) > T_{p2}(N)$ for all $N > n_0$. So as long as we know $T_{p1}$ is about $N^2$ and $T_{p2}$ is about $N$, then for sufficiently large $N$, $P_2$ will be faster.

### Formal Definitions

#### Big-Oh: $T(N) = O(f(N))$

**【Definition】** $T(N) = O(f(N))$ if there exist **positive constants** $c$ and $n_0$ such that:

$$T(N) \le c \cdot f(N) \quad \text{for all } N \ge n_0$$

This gives an **upper bound** on the growth rate.

#### Big-Omega: $T(N) = \Omega(g(N))$

**【Definition】** $T(N) = \Omega(g(N))$ if there exist **positive constants** $c$ and $n_0$ such that:

$$T(N) \ge c \cdot g(N) \quad \text{for all } N \ge n_0$$

This gives a **lower bound** on the growth rate.

#### Big-Theta: $T(N) = \Theta(h(N))$

**【Definition】** $T(N) = \Theta(h(N))$ **if and only if** $T(N) = O(h(N))$ **and** $T(N) = \Omega(h(N))$.

This gives a **tight bound** -- the function grows **exactly** like $h(N)$ (within constant factors).

#### Little-Oh: $T(N) = o(p(N))$

**【Definition】** $T(N) = o(p(N))$ if $T(N) = O(p(N))$ and $T(N) \neq \Theta(p(N))$.

This means $T(N)$ grows **strictly slower** than $p(N)$.

### Important Notes on Notation

- **Big-Oh convention (smallest upper bound):** $2N + 3 = O(N) = O(N^{k \ge 1}) = O(2^N) = \cdots$ We shall always take the **smallest** $f(N)$.
- **Big-Omega convention (largest lower bound):** $2N + N^2 = \Omega(2N) = \Omega(N^2) = \Omega(N) = \Omega(1) = \cdots$ We shall always take the **largest** $g(N)$.

---

### Growth Rate Diagram (from slides)

The diagram (Slide 11) shows the relative growth rates of common functions:

- **Log n** grows the slowest
- **n** grows linearly
- **n log n** grows faster than n but slower than n^2
- **n^2** grows quadratically
- **2^n** grows exponentially -- the fastest

**Ordered by growth rate (slowest to fastest):**

$$\log n \ll n \ll n \log n \ll n^2 \ll 2^n$$

---

### Rules of Asymptotic Notation

#### Rule 1: Addition and Multiplication

If $T_1(N) = O(f(N))$ and $T_2(N) = O(g(N))$, then:

- **(a)** $T_1(N) + T_2(N) = \max(O(f(N)), O(g(N)))$
  - The sum is dominated by the **larger** term.
- **(b)** $T_1(N) \cdot T_2(N) = O(f(N) \cdot g(N))$
  - The product multiplies.

#### Rule 2: Polynomial

If $T(N)$ is a **polynomial of degree $k$**, then:

$$T(N) = \Theta(N^k)$$

#### Rule 3: Logarithmic Growth

$$\log^k N = O(N) \quad \text{for any constant } k$$

This tells us that **logarithms grow very slowly** -- even polylogarithmic functions grow slower than any positive power of $N$.

#### Rule 4: Sufficiently Large N

When comparing the complexities of two programs asymptotically, make sure that $N$ is **sufficiently large**.

**Example:** Suppose $T_{p1}(N) = 10^6 N$ and $T_{p2}(N) = N^2$. Although $\Theta(N^2)$ grows faster than $\Theta(N)$, if $N < 10^6$, $P_2$ is still **faster** than $P_1$ in practice.

---

### Growth Rates and Time Table

The following table shows how long it takes for an algorithm of a given time complexity to process input of size $n$, assuming each operation takes $1 \mu s$ (microsecond $= 10^{-6}$ seconds).

**Units:** $\mu s = 10^{-6}$ seconds, $ms = 10^{-3}$ seconds, $sec = seconds$, $min = minutes$, $hr = hours$, $d = days$, $yr = years$

| n | O(log n) | O(n) | O(n log n) | O(n^2) | O(2^n) |
|---|----------|------|------------|--------|--------|
| Small | Instant | Fast | Moderate | Slow | Impractical |
| Medium | Instant | Moderate | Moderate | Slow | Impractical |
| Large | Instant | Slow | Slow | Very Slow | Impossible |

*(The exact values are shown in the slide table. The key takeaway is that exponential algorithms become unusable very quickly, while logarithmic algorithms remain fast even for enormous inputs.)*

---

### Example: Matrix Addition (Asymptotic Analysis)

```c
void add( int a[][MAX_SIZE],
          int b[][MAX_SIZE],
          int c[][MAX_SIZE],
          int rows, int cols )
{
    int i, j;
    for ( i = 0; i < rows; i++ )         /* Θ(rows) */
        for ( j = 0; j < cols; j++ )     /* Θ(rows * cols) */
            c[i][j] = a[i][j] + b[i][j]; /* Θ(rows * cols) */
}
```

$$T(rows, cols) = \Theta(rows \cdot cols)$$

---

## 4. General Rules for Analyzing Running Time

### Rule 1: FOR LOOPS

The running time of a `for` loop is **at most** the running time of the statements inside the `for` loop (including tests) **times** the number of iterations.

### Rule 2: NESTED FOR LOOPS

The total running time of a statement inside a group of nested loops is the running time of the statements **multiplied by the product** of the sizes of all the `for` loops.

### Rule 3: CONSECUTIVE STATEMENTS

These just **add** (which means that the **maximum** is the one that counts -- constants are dropped in asymptotic notation).

### Rule 4: IF / ELSE

For the fragment:

```
if ( Condition )   S1;
else               S2;
```

The running time is **never more than** the running time of the **test** plus the **larger** of the running time of $S_1$ and $S_2$.

### Rule 5: RECURSIONS

#### Example: Fibonacci Number

$$Fib(0) = Fib(1) = 1, \quad Fib(n) = Fib(n-1) + Fib(n-2)$$

```c
long int Fib( int N )
{
    if ( N <= 1 )
        return 1;          /* O(1) */
    else
        return Fib(N-1) + Fib(N-2);  /* T(N-1) + T(N-2) */
}
/* T(N) */
```

**Recurrence:**

$$T(N) = T(N-1) + T(N-2) + 2$$

**Analysis:**
- $T(N) \ge Fib(N)$ (proved by induction)
- $Fib(N)$ grows **exponentially**
- Therefore, $T(N)$ grows **exponentially** -- this is a very bad (inefficient) algorithm!

> **Question:** Why is it so bad? Because each call spawns two more calls, leading to exponential blow-up. The same subproblems are recomputed many times.

---

## 5. Comparing Algorithms: Maximum Subsequence Sum

**【Problem】** Given (possibly **negative**) integers $A_1, A_2, \dots, A_N$, find the maximum value of $\sum_{k=i}^{j} A_k$. The maximum sum is **0** if all integers are negative.

Four algorithms are presented, ranging from $O(N^3)$ to $O(N)$.

---

### Algorithm 1: Brute Force ($O(N^3)$)

```c
int MaxSubsequenceSum( const int A[], int N )
{
    int ThisSum, MaxSum, i, j, k;
/* 1*/  MaxSum = 0;                 /* initialize the maximum sum */
/* 2*/  for ( i = 0; i < N; i++ )   /* start from A[i] */
/* 3*/      for ( j = i; j < N; j++ ) {   /* end at A[j] */
/* 4*/          ThisSum = 0;
/* 5*/          for ( k = i; k <= j; k++ )
/* 6*/              ThisSum += A[k];  /* sum from A[i] to A[j] */
/* 7*/          if ( ThisSum > MaxSum )
/* 8*/              MaxSum = ThisSum;  /* update max sum */
            }  /* end for-j and for-i */
/* 9*/  return MaxSum;
}
```

**Complexity:** $T(N) = O(N^3)$

**Analysis:** Triple nested loops. For each starting index $i$ and ending index $j$, it recomputes the sum from scratch using a third loop.

---

### Algorithm 2: Improved Brute Force ($O(N^2)$)

```c
int MaxSubsequenceSum( const int A[], int N )
{
    int ThisSum, MaxSum, i, j;
/* 1*/  MaxSum = 0;                 /* initialize the maximum sum */
/* 2*/  for ( i = 0; i < N; i++ ) { /* start from A[i] */
/* 3*/      ThisSum = 0;
/* 4*/      for ( j = i; j < N; j++ ) {   /* end at A[j] */
/* 5*/          ThisSum += A[j];  /* sum from A[i] to A[j] incrementally */
/* 6*/          if ( ThisSum > MaxSum )
/* 7*/              MaxSum = ThisSum;  /* update max sum */
            }  /* end for-j */
        }  /* end for-i */
/* 8*/  return MaxSum;
}
```

**Complexity:** $T(N) = O(N^2)$

**Improvement:** Instead of recomputing the sum from scratch each time, it accumulates $A[j]$ incrementally (reuses the sum from $A[i]$ to $A[j-1]$).

---

### Algorithm 3: Divide and Conquer ($O(N \log N)$)

#### Strategy

1. **Divide:** Split the array into two halves.
2. **Conquer:** Recursively find the maximum subsequence sum in each half.
3. **Combine:** Find the maximum sum that **crosses** the midpoint (starts in left half, ends in right half).

The overall maximum is the max of:
- Max in left half
- Max in right half
- Max crossing sum

#### Diagram Description

The diagram (Slide 3) shows an array of 8 elements: `4, -3, 5, -2, -1, 2, 6, -2`.

- **Divide step:** Split into `[4, -3, 5, -2]` and `[-1, 2, 6, -2]`
- **Further divide:** Left half split into `[4, -3]` and `[5, -2]`; right half split into `[-1, 2]` and `[6, -2]`
- **Base cases:** `4`, `-3`, `5`, `-2`, `-1`, `2`, `6`, `-2`
- **Conquer upward:** Maximum of each section is computed recursively
  - `[4, -3]` max = 4
  - `[5, -2]` max = 5
  - Left half crossing max = max of `5+(-2)+(-3)=0`, `4+(-3)=1`, `5+(-2)=3`... the crossing max is `4 + (-3) + 5 = 6`
  - Left half overall max = 6
  - `[-1, 2]` max = 2
  - `[6, -2]` max = 6
  - Right half crossing max = 2 + 6 = 8
  - Right half overall max = 8
  - Overall crossing sum (crossing the main midpoint) = 4 + 7 = 11
  - Overall max = max(6, 8, 11) = 11
- The overall max subsequence sum is 11 (from `A[0]` through `A[6]`: 4 - 3 + 5 - 2 - 1 + 2 + 6 = 11)

#### Recurrence

$$T(N) = 2T(N/2) + cN, \quad T(1) = O(1)$$

#### Solution

$$
\begin{aligned}
T(N) &= 2T(N/2) + cN \\
     &= 2[2T(N/2^2) + cN/2] + cN \\
     &= 2^k O(1) + ckN \quad \text{where } N/2^k = 1 \\
     &= O(N \log N)
\end{aligned}
$$

**Complexity:** $T(N) = O(N \log N)$ (also true for $N \neq 2^k$).

*(The complete program can be found on p.21 of the textbook.)*

---

### Algorithm 4: On-line Algorithm ($O(N)$)

```c
int MaxSubsequenceSum( const int A[], int N )
{
    int ThisSum, MaxSum, j;
/* 1*/  ThisSum = MaxSum = 0;
/* 2*/  for ( j = 0; j < N; j++ ) {
/* 3*/      ThisSum += A[j];
/* 4*/      if ( ThisSum > MaxSum )
/* 5*/          MaxSum = ThisSum;
/* 6*/      else if ( ThisSum < 0 )
/* 7*/          ThisSum = 0;
        }  /* end for-j */
/* 8*/  return MaxSum;
}
```

**Complexity:** $T(N) = O(N)$

#### How It Works (Trace Example)

Given array: `-1, 3, -2, 4, -6, 1, 6, -1`

| j | A[j] | ThisSum (after) | MaxSum | ThisSum (reset if < 0) |
|---|------|-----------------|--------|----------------------|
| 0 | -1   | -1              | 0      | 0 |
| 1 | 3    | 3               | 3      | 3 |
| 2 | -2   | 1               | 3      | 1 |
| 3 | 4    | 5               | 5      | 5 |
| 4 | -6   | -1              | 5      | 0 |
| 5 | 1    | 1               | 5      | 1 |
| 6 | 6    | 7               | 7      | 7 |
| 7 | -1   | 6               | 7      | 6 |

Final answer: **7**

#### Key Properties

- **On-line** means: at any point in time, the algorithm can correctly give an answer to the subsequence problem for the data it has already read.
- The array $A[\,]$ is scanned **only once** (left to right).
- This is the **optimal** algorithm -- no algorithm can do better than $\Omega(N)$ since every element must be examined at least once.

---

### Running Time Comparison Table

| Algorithm | Time Complexity | N=10 | N=100 | N=1,000 | N=10,000 | N=100,000 |
|-----------|----------------|------|-------|---------|----------|-----------|
| **1** | $O(N^3)$ | 0.00103 s | 0.47015 s | 448.77 s | NA | NA |
| **2** | $O(N^2)$ | 0.00045 s | 0.01112 s | 1.1233 s | 111.13 s | NA |
| **3** | $O(N \log N)$ | 0.00066 s | 0.00486 s | 0.05843 s | 0.68631 s | 8.0113 s |
| **4** | $O(N)$ | 0.00034 s | 0.00063 s | 0.00333 s | 0.03042 s | 0.29832 s |

> **Note:** The time required to read the input is **not** included.

**Key observations:**
- Algorithm 4 ($O(N)$) scales almost linearly and handles N=100,000 in ~0.3 seconds.
- Algorithm 1 ($O(N^3)$) becomes unusable by N=1,000 (448 seconds).
- Algorithm 2 ($O(N^2)$) becomes slow by N=10,000 (111 seconds).
- Algorithm 3 ($O(N \log N)$) remains practical even for N=100,000.

---

## 6. Logarithms in the Running Time

### Example: Binary Search

#### Problem Statement

**Given:** A sorted array $A[0] \le A[1] \le \dots \le A[N-1]$ and a target value $X$.

**Task:** Find $X$ in the array.

**Output:**
- $i$ if $X == A[i]$
- $-1$ if $X$ is not found

#### Algorithm (Diagram Description)

The diagram (Slide 6) illustrates binary search with three pointers: `low`, `mid`, `high`.

1. Set `low = 0`, `high = N-1`.
2. Compute `mid = (low + high) / 2`.
3. Compare $X$ with $A[mid]$:
   - If **equal**: return `mid` (found).
   - If **less than**: set `high = mid - 1` (search left half).
   - If **greater than**: set `low = mid + 1` (search right half).
4. Repeat until `low > high` (not found).

#### Code Implementation

```c
int BinarySearch( const ElementType A[], ElementType X, int N )
{
    int Low, Mid, High;
/* 1*/  Low = 0;  High = N - 1;
/* 2*/  while ( Low <= High ) {
/* 3*/      Mid = ( Low + High ) / 2;
/* 4*/      if ( A[Mid] < X )
/* 5*/          Low = Mid + 1;
            else
/* 6*/          if ( A[Mid] > X )
/* 7*/              High = Mid - 1;
                else
/* 8*/              return Mid;  /* Found */
        }  /* end while */
/* 9*/  return NotFound;  /* NotFound is defined as -1 */
}
```

#### Complexity

$$T_{worst}(N) = O(\log N)$$

**Reasoning:** Each iteration halves the search space. After $k$ iterations, the search space is $N / 2^k$. When the search space reduces to 1, we have $N / 2^k = 1$, so $k = \log_2 N$.

#### When to Use Binary Search

Very useful when the data are **static** and in **sorted order** (e.g., finding words from a dictionary).

#### Homework (Self-Study)

- **Euclid's Algorithm** (for computing greatest common divisor) -- also $O(\log N)$
- **Exponentiation** (efficient power computation)

---

## 7. Checking Your Analysis

### Method 1: Ratio Test

After empirically measuring running times, check the ratio $T(2N) / T(N)$:

| If $T(N) = O(f(N))$ | Then $T(2N) / T(N) \approx$ |
|----------------------|--------------------------|
| $O(N)$ | 2 |
| $O(N^2)$ | 4 |
| $O(N^3)$ | 8 |
| $O(N \log N)$ | slightly more than 2 |
| $O(\log N)$ | slightly more than 1 |

### Method 2: Limit Test

When $T(N) = O(f(N))$, check if:

$$\lim_{N \to \infty} \frac{T(N)}{f(N)} \to \text{a positive constant}$$

*(See the example given on p.28, Figures 2.12 & 2.13 in the textbook.)*

---

## 8. Summary of Key Complexity Classes

| Complexity | Name | Description | Example |
|------------|------|-------------|---------|
| $O(1)$ | Constant | Time does not depend on input size | Array access |
| $O(\log N)$ | Logarithmic | Each step reduces problem size by a factor | Binary search |
| $O(N)$ | Linear | Single pass through data | On-line max subsequence sum |
| $O(N \log N)$ | Linearithmic | Divide and conquer | Merge sort, Algorithm 3 |
| $O(N^2)$ | Quadratic | Double nested loops | Algorithm 2, bubble sort |
| $O(N^3)$ | Cubic | Triple nested loops | Algorithm 1 |
| $O(2^N)$ | Exponential | Impractical for large N | Naive Fibonacci |

---

## Quick Reference: Asymptotic Notation Definitions

| Notation | Meaning | Formal Condition |
|----------|---------|------------------|
| $T(N) = O(f(N))$ | Upper bound ($\le$) | $\exists c, n_0 > 0: T(N) \le c \cdot f(N) \; \forall N \ge n_0$ |
| $T(N) = \Omega(g(N))$ | Lower bound ($\ge$) | $\exists c, n_0 > 0: T(N) \ge c \cdot g(N) \; \forall N \ge n_0$ |
| $T(N) = \Theta(h(N))$ | Tight bound ($=$) | $T(N) = O(h(N))$ and $T(N) = \Omega(h(N))$ |
| $T(N) = o(p(N))$ | Strictly slower ($<$) | $T(N) = O(p(N))$ and $T(N) \neq \Theta(p(N))$ |
